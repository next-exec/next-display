<template>
  <div id="app" class="container-fluid" style="height: 98vh; padding-top: 2vh; padding-left: 2vw;">
    <div class="row mb-4" style="height: 85%;">
      <div class="col-md-7" style="height: 100%; display: flex; flex-direction: column">
        <h1 class="mb-2">Announcements <span class="material-icons">announcement</span></h1>
        <div style="height: 100%; display: flex; flex-direction: column; justify-content: center">
        <p class="ml-2 mr-2" style="white-space: pre-line" v-if="currentAnnouncement.type == 'text'">{{ currentAnnouncement.data }}</p>
        <img v-else :src="currentAnnouncement.data"/>
        </div>
        <!--<div class="mt-4" style="background-repeat: no-repeat; background-size: contain; flex: 1;" :style="{'background-image': 'url(' + currentImage + ')'}">
        </div>-->
      </div>
      <div class="col-md-5" style="height: 100%; display: flex; flex-direction: column; justify-content: space-between; align-items: center;">
        <div>
          <h1>Weather <span class="material-icons">cloud</span></h1>
          <img :src="'http://openweathermap.org/img/w/' + weather.icon + '.png'" style="width: 25%;">
          <h2>{{ weather.temp + '°F' }}</h2>
          <h2>{{ weather.description }}</h2>
        </div>
        <div>
          <h1>Shuttles <span class="material-icons">airport_shuttle</span></h1>
          <h2 v-if="info.size === 0">No shuttles are currently being tracked.</h2>
          <table>
            <tbody>
              <tr v-for="item in info.values()" v-bind:key="item.title">
                <td style="text-align: left;"><h2>{{ item.title }}</h2></td>
                <td><h2>{{ item.prediction + ' ' + (item.prediction === '1' ? 'minute' : 'minutes') }}</h2></td>
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
      this.currentAnnouncementIndex = Math.floor(Math.random() * data.length);
      this.currentAnnouncement = data[this.currentAnnouncementIndex];
      setInterval(() => {
        this.currentAnnouncementIndex += 1;
        this.currentAnnouncementIndex %= data.length;
        this.currentAnnouncement = data[this.currentAnnouncementIndex];
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
