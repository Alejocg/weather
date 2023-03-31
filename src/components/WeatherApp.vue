<template>
  <div class="weather-app bg-gradient-to-r from-blue-500 via-blue-600 to-blue-700 text-white min-h-screen flex flex-col items-center justify-center">
    <h1 class="text-4xl font-bold mb-8">Weather App</h1>
    <div class="location-input mb-8 flex flex-col items-center sm:flex-row sm:justify-center sm:items-start">
      <label for="location-input" class="text-lg mb-2 sm:mr-2 sm:mb-0">Enter your location:</label>
      <div class="flex flex-col sm:flex-row items-center">
        <input type="text" id="location-input" v-model="location" class="border border-gray-300 rounded-l px-4 py-2 w-64 mr-2 text-blue-900 focus:outline-none focus:shadow-outline-blue" @keydown.enter="getWeather" />
        <div class="flex mt-2 sm:mt-0">
          <button @click="getWeather" class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 rounded-l shadow-md hover:shadow-lg transform hover:scale-105 transition duration-300 ease-in-out">
            Get Weather
          </button>
          <button @click="getLocation" class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 rounded-r shadow-md hover:shadow-lg transform hover:scale-105 transition duration-300 ease-in-out">
            Use Current Location
          </button>
        </div>
      </div>
    </div>
    <div v-if="weatherData" class="weather-info bg-blue-800 shadow-lg hover:shadow-xl rounded-lg p-6 w-80 transform hover:scale-105 transition duration-300 ease-in-out">
      <h2 class="text-2xl font-bold mb-4">{{ weatherData.name }}</h2>
      <div class="flex items-center mb-4">
        <img :src="getWeatherIconUrl(weatherData.weather[0].icon)" class="w-16 h-16 mr-4" />
        <p class="text-lg">{{ weatherData.weather[0].description }}</p>
      </div>
      <div class="flex justify-between mb-4">
        <div>
          <p class="text-4xl font-bold">{{ getTemperature() }}</p>
          <p class="text-lg">{{ getTemperatureUnit() }}</p>
        </div>
        <div>
          <button @click="toggleTemperatureUnit" class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 rounded-full shadow-md hover:shadow-lg transform hover:scale-105 transition duration-300 ease-in-out">
            {{ getTemperatureUnit(true) }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "WeatherApp",
  data() {
    return {
      location: "",
      weatherData: null,
      isCelsius: true,
    };
  },
  methods: {
    async getWeather() {
      this.weatherData = null; // set weatherData to null before making API call
      
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?${
          this.location ? `q=${this.location}` : ""
        }${
          this.location && "geolocation" in navigator ? "&" : ""
        }${
          "geolocation" in navigator ? "lat=" + this.latitude + "&lon=" + this.longitude : ""
        }&appid=60015bca9662a2ab816cbdd318050800&units=${
          this.isCelsius ? "metric" : "imperial"
        }`
      );
      const data = await response.json();
      this.weatherData = data;
    },
    getTemperature() {
      if (this.isCelsius) {
        return `${Math.round(this.weatherData.main.temp)}°C`;
      } else {
        return `${Math.round(this.weatherData.main.temp * 1.8 + 32)}°F`;
      }
    },
    getTemperatureUnit(short = false) {
      return this.isCelsius
        ? short
          ? "C"
          : "Celsius"
        : short
        ? "F"
        : "Fahrenheit";
    },
    toggleTemperatureUnit() {
      this.isCelsius = !this.isCelsius;
      this.getWeather();
    },
    getWeatherIconUrl(icon) {
      return `https://openweathermap.org/img/w/${icon}.png`;
    },
    getLocation() {
      if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition((position) => {
          this.latitude = position.coords.latitude;
          this.longitude = position.coords.longitude;
          this.location = "";
          this.getWeather();
        });
      } else {
        alert("Geolocation is not available");
      }
    },
  },
  computed: {
    isLocationEmpty() {
      return !this.location && !("geolocation" in navigator);
    },
  },
  watch: {
    location() {
      this.getWeather();
    },
  },
};
</script>


<style>
input:focus {
  outline: none;
  box-shadow: none;
}
</style>