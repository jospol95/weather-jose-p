<template>
  <div
    id="main"
    v-bind:style="{ backgroundImage: 'url(' + backgroundUrl + ')' }"
  >
    <WeatherMainComponent
      v-if="
        positionCoordinates.userLatitude != null &&
        positionCoordinates.userLatitude != null
      "
      :country-name="countryName"
      :city-name="cityName"
      :weather-details="weatherDetails"
      :opacity="opacity"
      @change-weather-unit-event="changeWeatherUnitHandler()"
    ></WeatherMainComponent>
    <div
      v-if="
        positionCoordinates.userLatitude == null ||
        positionCoordinates.userLatitude == null
      "
    >
      <h3>We were not able to calculate your location.</h3>
    </div>
  </div>
</template>

<script>
import WeatherMainComponent from "../components/WeatherMainComponent.vue";

export default {
  data() {
    return {
      apiKey: "14379316db7e5f1e4b19630f0bd124c9", //only for assignment purposes. but this should keep secret.
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
        country: String,
        city: String,
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
    callGeoLocationAndGetWeather: function () {
      // Initialize position
      this.positionCoordinates.userLatitude = null;
      this.positionCoordinates.userLongitude = null;
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.positionCoordinates.userLatitude = position.coords.latitude;
          this.positionCoordinates.userLongitude = position.coords.longitude;

          if (this.isSavedStateEmpty() || this.geoLocationHasChanged()) {
            this.saveState("positionCoordinates", this.positionCoordinates);
            this.getWeather();
            return;
          }

          //Coordinate hasn't changed, and we have a weather object saved in Local Storage.
          this.weatherDetails = JSON.parse(
            localStorage.getItem("weatherDetails")
          );
          this.setPreferredWeatherUnit();
          this.setBackgroundForWeather();
        },
        (error) => {
          console.log(error.message);
        }
      );
    },
    isSavedStateEmpty: function () {
      const coordinatesKey = localStorage.getItem("positionCoordinates");
      const weatherKey = localStorage.getItem("weatherDetails");
      return coordinatesKey == null || weatherKey == null;
    },
    geoLocationHasChanged: function () {
      const savedPositionCoordinates = JSON.parse(
        localStorage.getItem("positionCoordinates")
      );
      return (
        savedPositionCoordinates.userLatitude !==
          this.positionCoordinates.userLatitude ||
        savedPositionCoordinates.userLongitude !==
          this.positionCoordinates.userLongitude
      );
    },
    setPreferredWeatherUnit: function () {
      const preferredUnitKey = localStorage.getItem("preferredUnit");
      if (preferredUnitKey != null) {
        const preferredUnitValue = JSON.parse(preferredUnitKey);
        //fahrenheit by default. no need to convert.
        if (preferredUnitValue === "C") {
          this.convertToCelcius();
        }
      }
    },
    changeWeatherUnitHandler: function () {
      if (this.weatherDetails.units === "F") {
        this.convertToCelcius();
      } else {
        this.convertToFahrenheit();
      }
      this.saveState("preferredUnit", this.weatherDetails.units);
    },
    convertToCelcius: function () {
      this.weatherDetails.units = "C";
      this.weatherDetails.weather =
        (this.weatherDetails.weather - 32) * (5 / 9);
    },
    convertToFahrenheit: function () {
      this.weatherDetails.units = "F";
      this.weatherDetails.weather = this.weatherDetails.weather * (9 / 5) + 32;
    },
    saveState: function (keyName, keyValue) {
      localStorage.setItem(keyName, JSON.stringify(keyValue));
    },
    getWeather: function () {
      // We'll always ask for Fahrenheit (imperial), we'll make calculations in the system.
      const url = `https://api.openweathermap.org/data/2.5/weather?lat=${this.positionCoordinates.userLatitude}&lon=${this.positionCoordinates.userLongitude}&appid=${this.apiKey}&units=imperial`;
      fetch(url, {
        method: "GET",
      })
        .then((response) => response.json())
        .then((data) => {
          const regionNames = new Intl.DisplayNames(["en"], { type: "region" });
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
            country: regionNames.of(data.sys.country),
            city: data.name,
          };
          this.setBackgroundForWeather();
          this.saveState("weatherDetails", this.weatherDetails);
        });
    },
    setBackgroundForWeather: function () {
      // The idea here is to change the background depending on the weather state :) I tried to include all of them.
      const hours = new Date().getHours();
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
        case "Haze":
          this.backgroundUrl =
            "https://thumbs.gfycat.com/AbleClosedIndigowingedparrot-size_restricted.gif";
          this.opacity = 0.7;
          break;
        case "Clear":
          // eslint-disable-next-line no-case-declarations
          const dayTime = hours > 6 && hours < 19;
          if (!dayTime) {
            this.backgroundUrl =
              "https://media3.giphy.com/media/LoNF8cgrrwYju5DxQL/giphy.gif?cid=790b7611b7dd48ff3cf72f1e93f65404fa27ae500b9a36c6&rid=giphy.gif&ct=g";
            this.opacity = 0.7;
          } else {
            this.backgroundUrl =
              "https://www.coloradoboulevard.net/wp-content/uploads/2015/05/weather-sunny-day-gif.gif";
            this.opacity = 0.8;
          }
          break;
        default:
          this.opacity = 1;
          this.backgroundUrl =
            "https://www.solidbackgrounds.com/images/3840x2160/3840x2160-light-sky-blue-solid-color-background.jpg";
      }
    },
  },
  mounted() {
    //Call geoLocation. If geoLocation is denied, do nothing.
    this.callGeoLocationAndGetWeather();
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
