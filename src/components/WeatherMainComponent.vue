<template>
  <div
    v-if="weatherDetails != null"
    id="weather-tile"
    v-bind:style="{ opacity: opacity }"
  >
    <div class="weather-header section">
      <h2>CURRENT WEATHER</h2>
      <p class="weather-number">
        {{ Math.floor(weatherDetails.weather) }} &#176;{{
          weatherDetails.units
        }}
      </p>
      <span>{{ weatherDetails.state }}</span>
      <p>{{ weatherDetails.city }} | {{ weatherDetails.country }}</p>
    </div>
    <div class="weather-details section">
      <h4>HUMIDITY -> {{ weatherDetails.humidity }} %</h4>
      <p>WIND -> {{ weatherDetails.wind }} M/S</p>
      <p>SUNRISE -> {{ weatherDetails.sunriseDateTime }}</p>
      <p>SUNSET -> {{ weatherDetails.sunsetDateTime }}</p>
    </div>
    <div class="weather-footer section">
      <button @click="this.changeWeatherUnit()">
        Show me {{ this.showOppositeUnitToSelect() }}
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
      weather: 0,
    };
  },
  props: {
    opacity: Number,
    weatherDetails: {
      state: String,
      weather: Number,
      units: String,
      humidity: Number,
      wind: Number,
      sunriseDateTime: Date,
      sunsetDateTime: Date,
      city: String,
      country: String,
    },
  },
  methods: {
    showOppositeUnitToSelect: function () {
      if (this.weatherDetails.units === "F") {
        return "Celsius";
      }
      return "Fahrenheit";
    },
    changeWeatherUnit: function () {
      this.$emit("changeWeatherUnitEvent");
    },
  },
};
</script>

<style scoped>
#weather-tile {
  width: 400px;
  height: 400px;
  background: white;
  position: relative;
  margin: auto;
  transform: translate(0, 53%);
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
