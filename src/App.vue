<template>
  <div id="app">
    <div class="container">

      <div class="city flex">
        <p class="cityname">{{city}} </p>
        <div class="change">
          <button class="option" v-bind:class="{active: unit=='C', inactive: unit!='C'}" v-on:click="onChange('C')">C</button>
          <button class="option" v-bind:class="{active: unit=='F', inactive: unit!='F'}" v-on:click="onChange('F')">F</button>
        </div>
      </div>
      <div class="city panel">
        <button v-if="!isChangeCity" v-on:click="isChangeCity = true">Change the city</button>
        <div v-if="isChangeCity">
          <input v-model="cityNew" placeholder="Enter the city" />
          <button v-on:click="changeCity()">OK</button>
          <button v-on:click="changeCity(true)">Cancel</button>
        </div>
        <button v-on:click="getLocation()">
          <i class="fas fa-location-arrow" style="color: #fff;"></i>
          My location
        </button>
      </div>
        
      </div>

      <div class="weather">
        <div class="flex">
          <img class="temperature" v-bind:src="getUrl()" />
          <p class="temperature">{{temperature}}{{unit}}</p>
        </div>
        <p>{{weatherdescription}}</p>
      </div>

      <div class="additional_paramerets flex">
        <div class='param'>
          <p class="phenomenon">Wind</p>
          <p>{{wind}} m/s, direction {{partName}}</p>
        </div>
        <div class='param'>
          <p class="phenomenon">Atmospheric pressure</p>
          <p>{{pressure}} milimeters of mercury</p>
        </div>
        <div class='param'>
          <p class="phenomenon">Humidity</p>
          <p>{{humidity}}%</p>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  data () {
    return {
      weather: null,
      city: 'Krasnodar',
      weatherdescription: null,
      wind: null,
      part: null,
      pressure: null,
      humidity: null,
      temperature: null,
      temperatureK: null,
      unit: 'F',
      isCelsium : false,
      icon: null,
      isChangeCity: false,
      cityNew: null,
      partName: null
    }
  },
  mounted() {
    this.getCurrentWeather();
  },
  methods: {
        onChange: function(unit) {
            this.unit = unit;
            this.convert();
        },
        convert: function() {
          switch (this.unit) {
            case 'C':
              this.temperature = Math.round(this.temperatureK - 273.15);
              break;
              case 'F':
              this.temperature = Math.round((this.temperatureK - 273.15) * 1.8 + 32);
              break;
            default:
              break;
          }
        },
        getUrl: function() {
          return "http://openweathermap.org/img/wn/" + this.icon + "@2x.png";
        },
        changeCity: function(cancel) {
          if (!cancel) {
            this.city = this.cityNew;
            this.getCurrentWeather();

          } 
          this.cityNew = null;
          this.isChangeCity = false;
        },
        getCurrentWeather: function() {
          axios
            .get('http://api.openweathermap.org/data/2.5/forecast?q=' + this.city + '&APPID=cd8c8b627f351170401639aa10eee993')
            .then(response => {
              this.weather = response;
              this.city = response.data.city.name;
              this.weatherdescription = response.data.list[0].weather[0].description;
              this.wind = response.data.list[0].wind.speed;
              this.part = response.data.list[0].wind.deg;
              this.pressure = response.data.list[0].main.pressure;
              this.humidity = response.data.list[0].main.humidity;
              this.temperatureK = response.data.list[0].main.temp;
              this.icon = response.data.list[0].weather[0].icon;
              this.convert();
              this.getWindDirection();
            });
        },
        getLocation: function() {
          axios
            .get('http://ip-api.com/json')
            .then(response => {
              console.log("response", response);
              this.city = response.data.city;
              this.getCurrentWeather();
            });
        },
        getWindDirection: function() {
          switch (Math.round(this.part/360*7)) {
            case 0:
              this.partName = "North";
              break;
            case 1:
              this.partName = "North-East";
              break;
            case 2:
              this.partName = "East";
              break;
            case 3:
              this.partName = "South-East";
              break;
            case 4:
              this.partName = "South";
              break;
            case 5:
              this.partName = "South-West";
              break;
            case 6:
              this.partName = "West";
              break;
            case 7:
              this.partName = "North-West";
              break;
            default:
              break;
          }
        }
    }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #fff;
  padding: 0;
  margin-top: 4em;
}

body {
  background: #048bef;
  height: 100%;
}

h1, h2 {
  font-weight: normal;
}

p {
  margin: 0;
  line-height: 1.5em;
  font-size: 0.9em;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0;
}

.panel {
  display: flex;
  justify-content: flex-start;
}

a {
  color: #fff;
  text-decoration: none;
}

.cityname {
  font-size: 2em;
}

button {
    background: #048bef;
    border: none;
    color: #fff;
    cursor: pointer;
}

.temperature {
  font-size: 4em;
  display: inline-block;
}

.container {
    width: 70%;
    margin: 0 auto;
    border: none;
}

.flex {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
}

.weather {
  text-align: center;
  padding: 8em 0;
}

.phenomenon {
  font-size: 0.6em;
}

.change button {
    width: 2em;
    height: 2em;
}

.change {
    border: 0.09em solid #fff;
    border-radius: 3px;
    height: 100%;
}

.param {
  width: 30%;
  text-align: center;
}

.option:first-child {
  border-right: none;
}

.option:last-child {
  border-left: none;
}

.active{
    background-color: #078dee;
}

.inactive {
  background-color: #678dbc;
  height: 100%;
}

.weather .flex {
align-items: stretch;
justify-content: center;
}

@media (max-width: 720px) {
  #app {
    margin: 0;
  }

  .container {
    width: 90%;
  }

  body {
    background-color: #678dbc;
  }

  button {
    background-color: #678dbc;
    letter-spacing: 2px;
  }

  .active{
    background-color: #7fa4c6;
  }

  .param {
    text-align: center;
    padding: 1em;
  }

  .panel {
    display: flex;
    justify-content: space-between;
  }
}
</style>

