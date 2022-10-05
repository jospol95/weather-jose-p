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
    <!--    Control farenheit/celsius from parent, save desired opt in LocalStorage-->
  </div>
</template>

<script>
import WeatherMainComponent from "../components/WeatherMainComponent.vue";

export default {
  data() {
    return {
      apiKey: "14379316db7e5f1e4b19630f0bd124c9",
      backgroundUrl: String,
      opacity: Number,
      countryName: String,
      cityName: String,
      weatherDetails: {
        state: String,
        weather: Number,
        units: String,
        humidity: Number,
        wind: Number,
        sunriseDateTime: String,
        sunsetDateTime: String,
      },
      positionCoordinates: {
        userLatitude: Number,
        userLongitude: Number,
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
          this.positionCoordinates.userLatitude = position.coords.latitude;
          this.positionCoordinates.userLongitude = position.coords.longitude;
          const coordinatesKey = localStorage.getItem("positionCoordinates");
          const weatherKey = localStorage.getItem("weatherDetails");
          if (coordinatesKey == null || weatherKey == null) {
            this.savePositionCoordinatesState();
            this.getWeather();
            return;
          }
          const savedPositionCoordinates = JSON.parse(coordinatesKey);
          if (
            savedPositionCoordinates.userLatitude !==
              this.positionCoordinates.userLatitude &&
            savedPositionCoordinates.userLongitude !==
              this.positionCoordinates.userLongitude
          ) {
            this.savePositionCoordinatesState();
            this.getWeather();
            return;
          }
          //Coordinate hasn't changed, and we have a weather object save in Local Storage.
          this.weatherDetails = JSON.parse(weatherKey);
          this.setBackgroundForWeather();
        },
        (error) => {
          console.log(error.message);
        }
      );
    },
    savePositionCoordinatesState: function () {
      localStorage.setItem(
        "positionCoordinates",
        JSON.stringify(this.positionCoordinates)
      );
    },
    saveWeatherState: function () {
      localStorage.setItem(
        "weatherDetails",
        JSON.stringify(this.weatherDetails)
      );
    },
    doReverseGeoLocation: function () {
      //To get city, country
      this.cityName = "San Salvador";
      this.countryName = "El Salvador";
    },
    getWeather: function () {
      //We'll always ask for Fahrenheit (imperial), we'll make calculations in the system.
      const url = `https://api.openweathermap.org/data/2.5/weather?lat=${this.positionCoordinates.userLatitude}&lon=${this.positionCoordinates.userLongitude}&appid=${this.apiKey}&units=imperial`;
      fetch(url, {
        method: "GET",
      })
        .then((response) => response.json())
        .then((data) => {
          const sunriseDateTime = new Date(data.sys.sunrise * 1000);
          const sunsetDateTime = new Date(data.sys.sunset * 1000);
          this.weatherDetails = {
            state: data.weather[0].main,
            weather: data.main.temp,
            units: "F",
            wind: data.wind.speed,
            humidity: data.main.humidity,
            sunriseDateTime: `${sunriseDateTime.getHours()}:${sunsetDateTime.getMinutes()}`,
            sunsetDateTime: `${sunsetDateTime.getHours()}:${sunsetDateTime.getMinutes()}`,
          };
          this.setBackgroundForWeather();
          this.saveWeatherState();
        });
    },
    setBackgroundForWeather: function () {
      switch (this.weatherDetails.state) {
        case "Clouds":
          this.backgroundUrl =
            "https://i.gifer.com/origin/dd/ddedd3a2f4a3995d8cd1a8ab2033c9ce.gif";
          this.opacity = 0.7;
          break;
        case "Thunderstorm":
        case "Drizzle":
        case "Rain":
        case "Snow":
          this.backgroundUrl =
            "https://thumbs.gfycat.com/AbleClosedIndigowingedparrot-size_restricted.gif";
          this.opacity = 0.7;
          break;
        case "Clear":
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
    this.doReverseGeoLocation();
  },
};
</script>

<style>
#main {
  font-family: "Open Sans", sans-serif;
  text-align: center;
  height: 80vh;
  background-size: 600px 600px;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
}
</style>
