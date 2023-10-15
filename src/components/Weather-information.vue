<template>
  <div class="container">
    <div class="inner-container">
      <h2>{{ temperatureInCelsius }}<span>&#176;</span>C</h2>
      <p>{{ weatherDescription }}</p>
      <div v-if="showcloudy" class="weather-condition">
        <img :src="require('@/assets/cloudy.png')" alt="">
      </div>
      <div v-if="showRainy" class="weather-condition">
        <img :src="require('@/assets/rainy.png')" alt="">
      </div>
      <div v-if="showclear" class="weather-condition">
        <img :src="require('@/assets/clear.png')" alt="">
      </div>
      <div v-if="showstormy" class="weather-condition">
        <img :src="require('@/assets/storm.png')" alt="">
      </div>
      <div class="input-container">
        <label for="latitude">Latitude</label>
        <input type="text" name="latitude" v-model="latitude">
      </div>
      <div class="input-container">
        <label for="longitude">Longitude</label>
        <input type="text" name="longitude" v-model="longitude">
      </div>
      <button @click="GetWeatherData">Get Data</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      weather: {},
      weatherDescription: '',
      showcloudy: false,
      showRainy: false,
      showclear: false,
      showstormy: false,
      temperatureInCelsius: '',
      latitude: '38',
      longitude: '78',
    }
  },
  methods: {
    GetWeatherData() {
      axios
        .get('https://api.openweathermap.org/data/2.5/weather?lat=' + this.latitude + '&lon=' + this.longitude + '&appid=29afbf476d234c47ff47b79a10f77c00')
        .then((response) => {
          console.log(response.data);
          let maindescription = response.data.weather[0].main.toLowerCase();
          let descriptionString = response.data.weather[0].description;
          this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
          this.temperatureInCelsius = (response.data.main.temp - 273.15).toFixed(2);

          // Reset all weather conditions
          this.showcloudy = false;
          this.showRainy = false;
          this.showclear = false;
          this.showstormy = false;

          switch (maindescription) {
            case 'clouds':
              this.showcloudy = true;
              break;
            case 'thunderstorm':
              this.showstormy = true;
              break;
            case 'rain':
              this.showRainy = true;
              break;
            case 'clear':
              this.showclear = true;
              break;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.GetWeatherData(); // Automatically fetch data when the component is mounted
  },
}
</script>

<style>
.container {
  width: 100%;
  display: flex;
  justify-content: center;
}
.inner-container {
  width: 50%;
  background-color: rgb(202, 213, 250);
}
</style>
