<template>
  <div id="weather-tile" v-bind:style="{ opacity: opacity }">
    <div class="weather-header section">
      <h2>CURRENT WEATHER</h2>
      <p class="weather-number">
        {{ Math.round(weather * 100) / 100 }} &#176;{{ weatherUnit }}
      </p>
      <span>{{ weatherDetails.state }}</span>
      <p>{{ cityName }} | {{ countryName }}</p>
    </div>
    <div class="weather-details section">
      <h4>HUMIDITY {{ weatherDetails.humidity }} %</h4>
      <p>WIND {{ weatherDetails.wind }} M/S</p>
      <p>SUNRISE {{ weatherDetails.sunriseDateTime }}</p>
      <p>SUNSET {{ weatherDetails.sunsetDateTime }}</p>
    </div>
    <div class="weather-footer section">
      <button @click="this.changeWeatherUnit()">
        Show me {{ this.showOppositeUnitSelected() }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
      backgroundUrl: String,
      weatherUnit: String,
      weather: Number,
    };
  },
  props: {
    countryName: String,
    cityName: String,
    opacity: Number,
    // cardOpacity: Number,
    weatherDetails: {
      state: String,
      weather: Number,
      units: String,
      humidity: Number,
      wind: Number,
      sunriseDateTime: Date,
      sunsetDateTime: Date,
    },
  },
  methods: {
    showOppositeUnitSelected: function () {
      if (this.weatherUnit === "F") {
        return "Celsius";
      }
      return "Fahrenheit";
    },
    changeWeatherUnit: function () {
      if (this.weatherUnit === "F") {
        this.weatherUnit = "C";
        this.weather = (this.weather - 32) * (5 / 9);
      } else {
        this.weatherUnit = "F";
        this.weather = this.weather * (9 / 5) + 32;
      }
    },
  },
  beforeMount() {
    debugger;
    this.weather = this.weatherDetails.weather;
    this.weatherUnit = this.weatherDetails.units;
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#weather-tile {
  width: 400px;
  height: 400px;
  background: white;
  position: relative;
  top: 21%;
  left: 34%;
}

h2 {
  margin-bottom: 0;
}

h4 {
  margin: 0;
}

p {
  margin: 3px;
}

p.weather-number {
  font-size: 50px;
  margin-bottom: 0;
  font-weight: bold;
}

span {
  margin: 0;
  font-size: 14px;
}

.section {
  margin-bottom: 40px;
}
</style>
