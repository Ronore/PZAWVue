<template>
    <div class="weather-app" :style="{ backgroundColor: backgroundColor }">
        <!-- Search Bar at the Top -->
        <div class="form-container">
            <form @submit.prevent="searchLocation">
                <input type="text" v-model="locationSearch" class="search-input" placeholder="Enter Location..." />
                <button type="submit" class="search-button">Search</button>
            </form>
        </div><br>

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
            return '#FFD700'; // Gold for warm temperatures
        } else if (main.value.temp > 10) {
            return '#ADD8E6'; // Light Blue for moderate temperatures
        } else {
            return '#87CEEB'; // Sky Blue for cold temperatures
        }
    }
    return '#FFFFFF'; // Default to white if no temperature data
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
.dynamic-bg {
    background-color: var(--bg-color);
}

.weather-app {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

:root {
    --cold-bg-color: #ADD8E6;
    --warm-bg-color: #FFD700;
    --hot-bg-color: #FF4500;
}

.title {
    background-color: #007bff;
    border-radius: 10px;
    width: 90%;
    max-width: 600px;
    padding: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
    /* Space between elements */
    margin-top: 20px;
    /* Top margin to separate from other content */
}

/* Input field styles */
.search-input {
    padding: 8px 12px;
    /* Padding around the text */
    border: 1px solid #ccc;
    /* Border around the input */
    border-radius: 4px;
    /* Rounded corners */
    outline: none;
    /* Removes the default focus outline */
    transition: border-color 0.3s ease;
    /* Smooth transition for hover effect */
}

.search-input:focus {
    border-color: #007bff;
    /* Changes border color on focus */
}

/* Button styles */
.search-button {
    padding: 8px 16px;
    /* Padding around the text */
    background-color: #007bff;
    /* Background color */
    color: white;
    /* Text color */
    border: none;
    /* Removes the default button border */
    border-radius: 4px;
    /* Rounded corners */
    cursor: pointer;
    /* Changes cursor to pointer on hover */
    transition: background-color 0.3s ease;
    /* Smooth transition for hover effect */
}

.search-button:hover {
    background-color: #0056b3;
    /* Darkens the background on hover */
}
</style>
