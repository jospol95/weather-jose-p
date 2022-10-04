<template>
  <div
    id="main"
    v-bind:style="{ backgroundImage: 'url(' + backgroundUrl + ')' }"
  >
    <WeatherMainComponent
      :country-name="countryName"
      :city-name="cityName"
      :weather-details="weatherDetails"
      :opacity="opacity"
    ></WeatherMainComponent>
  </div>
</template>

<script>
import WeatherMainComponent from "../components/WeatherMainComponent.vue";
// import MainTemplateComponent from "./components/MainTemplateComponent.vue";

export default {
  data() {
    return {
      backgroundUrl: String,
      opacity: Number,
      userLatitude: Number,
      userLongitude: Number,
      countryName: String,
      cityName: String,
      weatherDetails: {
        state: String,
        weather: Number,
        degreeType: String,
        Humidity: Number,
        Wind: Number,
        SunriseDateTime: Date,
        SunsetDateTime: Date,
      },
    };
  },
  name: "WeatherMain",
  components: {
    WeatherMainComponent,
  },
  methods: {
    callGeoLocation: function () {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.userLatitude = position.coords.latitude;
          this.userLongitude = position.coords.longitude;
        },
        (error) => {
          console.log(error.message);
        }
      );
    },
    doReverseGeoLocation: function () {
      //To get city, country
      this.cityName = "San Salvador";
      this.countryName = "El Salvador";
    },
    getWeather: function () {
      //To get weather
      this.weatherDetails.weather = 22;
      this.weatherDetails.degreeType = "F";
      this.weatherDetails.state = "Rainy";
    },
    setBackgroundForWeather: function () {
      switch (this.weatherDetails.state) {
        case "Cloudy":
          this.backgroundUrl =
            "https://i.gifer.com/origin/dd/ddedd3a2f4a3995d8cd1a8ab2033c9ce.gif";
          this.opacity = 0.7;
          break;
        case "Rainy":
          this.backgroundUrl =
            "https://thumbs.gfycat.com/AbleClosedIndigowingedparrot-size_restricted.gif";
          this.opacity = 0.7;
          break;
        case "Sunny":
          this.backgroundUrl =
            "https://www.coloradoboulevard.net/wp-content/uploads/2015/05/weather-sunny-day-gif.gif";
          this.opacity = 0.8;
          break;
        case "CleanNight":
          this.backgroundUrl =
            "https://media3.giphy.com/media/LoNF8cgrrwYju5DxQL/giphy.gif?cid=790b7611b7dd48ff3cf72f1e93f65404fa27ae500b9a36c6&rid=giphy.gif&ct=g";
          this.opacity = 0.5;
          break;
      }
    },
  },
  beforeMount() {
    //Call geoLocation. If geoLocation is denied, do nothing.
    //If geoLocation good, call getWeather and revereGeoLocation.
    this.callGeoLocation();
    this.getWeather();
    this.doReverseGeoLocation();
    this.setBackgroundForWeather();
  },
};
</script>

<style>
#main {
  font-family: "Open Sans", sans-serif;
  text-align: center;
  /*background-color: red;*/
  height: 80vh;
  /*background: url("https://thumbs.gfycat.com/AbleClosedIndigowingedparrot-size_restricted.gif")*/
  /*  no-repeat fixed center;*/
  /*background: url("https://i.gifer.com/origin/dd/ddedd3a2f4a3995d8cd1a8ab2033c9ce.gif")*/
  /*  no-repeat fixed center;*/
  /*background: url("https://www.coloradoboulevard.net/wp-content/uploads/2015/05/weather-sunny-day-gif.gif")*/
  /*  no-repeat fixed center;*/
  /*background: url("https://media3.giphy.com/media/LoNF8cgrrwYju5DxQL/giphy.gif?cid=790b7611b7dd48ff3cf72f1e93f65404fa27ae500b9a36c6&rid=giphy.gif&ct=g")*/
  /*  no-repeat fixed center;*/
  background-size: 600px 600px;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
}
</style>
