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
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.positionCoordinates.userLatitude = position.coords.latitude;
          this.positionCoordinates.userLongitude = position.coords.longitude;

          if (this.isSavedStateEmpty() || this.geoLocationHasChanged()) {
            this.saveState("positionCoordinates", this.positionCoordinates);
            this.getWeather();
            return;
          }

          //Coordinate hasn't changed, and we have a weather object save in Local Storage.
          this.weatherDetails = JSON.parse(
            localStorage.getItem("weatherDetails")
          );
          this.setPreferredWeatherUnit();
          this.setBackgroundForWeather();
        },
        (error) => {
          console.log(error.message);
          this.positionCoordinates.userLatitude = null;
          this.positionCoordinates.userLongitude = null;
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
      //We'll always ask for Fahrenheit (imperial), we'll make calculations in the system.
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
          if (new Date().getHours() > 6) {
            //Check if nighttime.
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
            "data:image/jpeg;base64,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";
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
