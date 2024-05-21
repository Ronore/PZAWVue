<template>
  <div class="weather-app" :style="{ backgroundColor: backgroundColor }">
    <!-- Search Bar at the Top -->
    <div class="form-container">
      <form @submit.prevent="searchLocation">
        <input type="text" v-model="locationSearch" class="search-input" placeholder="Enter Location..." />
        <button type="submit" class="search-button">Search</button>
      </form>
    </div>

    <!-- Weather Display -->
    <div class="title" v-if="weather.locationName">
      <h1>Prognoza Pogody: {{ weather.locationName }}</h1>
      <div class="weather-info">
        <p>{{ weather.description }}</p>
        <img :src="weatherIconUrl" alt="Weather Icon" class="weather-icon">
      </div>
      <div class="weather-details">
        <p>Temperatura: {{ main.temp }} °C</p>
        <p>Ciśnienie atmosferyczne: {{ main.pressure }} hPa</p>
        <p>Wilgotność powietrza: {{ main.humidity }}%</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';

const forecasts = ref([]);
const weather = ref({});
const main = ref({});
const data = ref({});
const locationSearch = ref('');

const fallbackData = {
  //... (same as provided)
};

const getLocationCoordinates = async (locationName) => {
  try {
    if (locationName === "") {
      locationName = "Węgorzyno";
    }
    const response = await fetch(`http://api.openweathermap.org/geo/1.0/direct?appid=5f537523fc3983aa94364a31041956d3&limit=2&q=${encodeURIComponent(locationName)}`);
    const data = await response.json();
    if (data.length > 0) {
      return { lat: data[0].lat, lon: data[0].lon };
    }
  } catch (error) {
    console.error('Error getting location coordinates:', error);
  }
  return null;
};

const getWeatherForecast = async () => {
  try {
    const coords = await getLocationCoordinates(locationSearch.value);
    if (!coords) {
      throw new Error('Location not found');
    }
    const response = await fetch(`http://api.openweathermap.org/data/2.5/forecast/?cnt=1&lat=${coords.lat}&lon=${coords.lon}&units=metric&lang=pl&appid=5f537523fc3983aa94364a31041956d3`);
    const newData = await response.json();
    forecasts.value = newData;
    weather.value = newData.list[0].weather[0];
    main.value = newData.list[0].main;
    weather.value.locationName = locationSearch.value;
  } catch (error) {
    console.error('Error fetching weather forecast:', error);
    forecasts.value = fallbackData;
    weather.value = fallbackData.list[0].weather[0];
    main.value = fallbackData.list[0].main;
  }
};

const backgroundColor = computed(() => {
  if (main.value && main.value.temp) {
    if (main.value.temp >= 25) {
      return '#FFD700';
    } else if (main.value.temp > 10) {
      return '#ADD8E6';
    } else {
      return '#87CEEB';
    }
  }
  return '#FFFFFF';
});

onMounted(() => {
  getWeatherForecast();
});

const weatherIconUrl = computed(() => {
  if (weather.value && weather.value.icon) {
    return `https://openweathermap.org/img/wn/${weather.value.icon}.png`;
  }
  return '';
});

const searchLocation = async () => {
  getWeatherForecast();
};
</script>

<style scoped>
/* Updated background colors for different temperature ranges */
:root {
  --cold-bg-color: #ADD8E6;
  --warm-bg-color: #d9e40c;
  --hot-bg-color: #FF4500;
  --neutral-bg-color: #F0F8FF;
  --dark-bg-color: #333;
}

.title {
  background-color: #98b4c5;
  border-radius: 10px;
  width: 90%;
  max-width: 600px;
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  margin-top: 60px;
  z-index: 0;
}

.weather-app {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-color: #1e5712;
  color: #FFF;
}

h1 {
  text-align: center;
  color: white;
}

.weather-info {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
}

.weather-icon {
  width: 50px;
  height: auto;
}

.weather-details p {
  font-size: 18px;
  color: white;
  margin-bottom: 10px;
}

.form-container {
  position: absolute;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1;
}

.search-input {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.3s ease;
  color: #333;
}

.search-input:focus {
  border-color: #007bff;
}

.search-button {
  padding: 8px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-button:hover {
  background-color: #0056b3;
}
</style>
