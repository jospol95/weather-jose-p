<template>
  <div id="weather-tile" v-bind:style="{ opacity: opacity }">
    <div class="weather-header section">
      <h2>CURRENT WEATHER</h2>
      <p class="weather-number">
        {{ weatherDetails.weather }} &#176;{{ weatherDetails.degreeType }}
      </p>
      <span>{{ weatherDetails.state }}</span>
      <p>{{ cityName }} | {{ countryName }}</p>
    </div>
    <div class="weather-details section">
      <h4>HUMIDITY 73%</h4>
      <p>WIND 6.2 M/S</p>
      <p>SUNRISE 05:41</p>
      <p>SUNSET 20:06</p>
    </div>
    <div class="weather-footer section">
      <p>Toggle</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
      backgroundUrl: String,
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
      degreeType: String,
      Humidity: Number,
      Wind: Number,
      SunriseDateTime: Date,
      SunsetDateTime: Date,
    },
  },
  methods: {},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#weather-tile {
  /*opacity: 0.7;*/
  width: 400px;
  height: 400px;
  background: white;
  /*margin: 0;*/
  /*position: absolute;*/
  position: relative;
  top: 20%;
  left: 37%;
  /*-ms-transform: translate(-50%, -50%);*/
  /*transform: translate(-50%, -50%);*/
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
