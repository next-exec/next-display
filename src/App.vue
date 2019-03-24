<template>
  <div id="app" class="container-fluid" style="height: 98vh; padding-top: 2vh; padding-left: 2vw;">
    <div class="row" style="height: 85%;">
      <div class="col-md-7" style="height: 100%;">
        <h1>Announcements <span class="material-icons">announcement</span></h1>
      </div>
      <div class="col-md-5">
        <div>
          <h1>Weather <span class="material-icons">cloud</span></h1>
        </div>
        <div>
          <h1>Shuttles <span class="material-icons">airport_shuttle</span></h1>
        </div>
      </div>
    </div>
    <div class="row" style="height: 15%; padding-top: 1vw;">
      <div class="col-md-12">
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
export default class App extends Vue {}
</script>

<style>
h1 {
  font-size: 3.5em;
  text-align: left;
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
