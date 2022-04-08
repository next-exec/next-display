<template>
  <div id="app" class="container-fluid" style="height: 98vh; padding-top: 2vh; padding-left: 2vw;">
    <div class="row mb-4" style="height: 85%;">
      <div class="col-md-8" style="height: 100%; display: flex; flex-direction: column">
        <h1 class="mb-2">Announcements <span class="material-icons">announcement</span></h1>
        <div style="height: 100%; display: flex; flex-direction: column; justify-content: center">
        <p class="ml-2 mr-2" style="white-space: pre-line" v-if="currentAnnouncement.type == 'text'">{{ currentAnnouncement.data }}</p>
        <img v-else :src="currentAnnouncement.data"/>
        </div>
        <!--<div class="mt-4" style="background-repeat: no-repeat; background-size: contain; flex: 1;" :style="{'background-image': 'url(' + currentImage + ')'}">
        </div>-->
      </div>
      <div class="col-md-4" style="height: 100%; display: flex; flex-direction: column; justify-content: space-between; align-items: center;">
        <div>
          <h1 style="text-align: center;">Weather <span class="material-icons">cloud</span></h1>
          <img :src="'http://openweathermap.org/img/w/' + weather.icon + '.png'" style="width: 25%;">
          <h2>{{ weather.temp + '°F' }}</h2>
          <h2>{{ weather.description }}</h2>
        </div>
        <div>
          <h1 style="text-align: center;">Shuttles <span class="material-icons">airport_shuttle</span></h1>
          <h3 v-if="info.size === 0">No shuttles are currently being tracked.</h3>
          <table>
            <tbody>
              <tr v-for="item in info.values()" v-bind:key="item.title">
                <td style="text-align: left;"><h3>{{ item.title }}</h3></td>
                <td><h3>{{ item.prediction + ' ' + (item.prediction === '1' ? 'minute' : 'minutes') }}</h3></td>
              </tr>
            </tbody>
          </table>
        </div>
        <div>
          <h1 style="text-align: center;">Coming Up <span class="material-icons">schedule</span></h1>
          <table>
            <tbody>
              <tr v-for="upcoming in upcoming.values()" v-bind:key="upcoming.title">
                <td style="text-align: left;"><h3>{{ upcoming.title }}</h3></td>
                <td><h3>{{ `${new Date(upcoming.start).toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' })} (${upcoming.location})` }}</h3></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <div class="row" style="height: 15%; padding-top: 1vw;">
      <div class="col-md-12">
        <h2 style="text-align: left;">{{ time }}</h2>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

import './app.css'

@Component({
  components: {}
})
export default class App extends Vue {
  private currentAnnouncement: {type: "image" | "text", data: string} = { type: "text", data: 'hello!'};
  private currentAnnouncementIndex: number = 0;
  private announcements: Array<{type: "image" | "text", data: string}> = [];
  private events: Array<{start: string, end: string, location: string, title: string}> = [];
  private upcoming: Array<{start: string, end: string, location: string, title: string}> = [];
  private error: boolean = false
  private info: Map<string, { prediction: string, title: string }> = new Map()
  private time: string = ''
  private weather: { description: string, icon: string, temp: number } = { description: '', icon: '', temp: 0 }
  private weatherError: boolean = false

  private mounted (): void {
    setInterval(() => {
      this.time = new Date().toLocaleString()
    }, 1000)
    fetch('weather.py').then((data) => {
      return data.json()
    }).then((data) => {
      this.weather = {
        description: data.weather[0].description,
        icon: data.weather[0].icon,
        temp: Math.floor(data.main.temp)
      }
    }).catch((error) => {
      console.log(error)
      this.weatherError = true
    })

    fetch('https://retro.umoiq.com/service/publicJSONFeed?command=predictionsForMultiStops&a=mit' +
      '&stops=tech|tangwest&stops=saferidecampshut|tangwest').then((data) => {
        return data.json()
      }).then((data) => {
        const info = new Map()
        for (const route of data.predictions) {
          if (route.direction !== undefined) {
            info.set(route.routeTag, {
              prediction: route.direction.prediction[0].minutes,
              title: route.routeTitle
            })
          }
        }
        this.info = info
      }).catch((error) => {
        this.error = true
        console.log(error)
      })

    fetch('config.json').then((data) => {
      return data.json();
    }).then((data) => {
      this.events = data.events;
      this.announcements = data.announcements;

      let events = this.events.filter(event => (new Date(event.start) < new Date()) && (new Date() < new Date(event.end)));

      if (this.announcements.length == 0 || data.mixEvents){
        for(let event of events){
          this.announcements.push({type: "text", data: `Happening now: ${event.title} (${event.location})`})
        }
      }

      let upcoming = this.events.filter(event => new Date() < new Date(event.start)).sort(event => new Date(event.start).valueOf()- new Date().valueOf());
      this.upcoming = upcoming.slice(0, 3);
      
      this.currentAnnouncementIndex = Math.floor(Math.random() * this.announcements.length);
      this.currentAnnouncement = this.announcements[this.currentAnnouncementIndex];
      setInterval(() => {
        this.currentAnnouncementIndex += 1;
        this.currentAnnouncementIndex %= this.announcements.length;
        this.currentAnnouncement = this.announcements[this.currentAnnouncementIndex];
      }, 10000)
    }).catch((error) => {
      console.log(error)
    })
  }
}
</script>

<style>
h1 {
  font-size: 5em;
  text-align: left;
}

h2 {
  font-size: 3em;
}

p {
  font-size: 4em;
  text-align: center;
}

table {
  width: 100%;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /*margin-top: 60px;*/
}
</style>
