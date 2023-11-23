<template>
    <div class="flex flex-col flex-1 items-center">
      <!-- Banner -->
      <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
        <p>
          You are currently preview this city, click the "+",
          icon to start tracking this city.
        </p>
      </div>
      <!-- Weather Overview-->
      <div class="flex flex-col items-center text-white py-12">
        <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
        <p class="text-sm mb-12">
          {{
            new Date(weatherData.currentTime).toLocaleDateString(
              "it-it",
              {
                weekday: "short",
                day: "2-digit",
                month: "short",
              })
          }}
          {{
            new Date(weatherData.currentTime).toLocaleTimeString(
              "it-it",
              {
                timeStyle: "short",
              })
          }}
        </p>
        <p class="text-8xl mb-8">
          {{ Math.round((weatherData.main.temp-32)/1.8) }}&deg;
        </p>
        <div class="text-center">
          <p class="text-center">
            Feels like
            {{ Math.round((weatherData.main.feels_like-32)/1.8) }} &deg;
          </p>
          <p class="capitalize">
            {{ weatherData.weather[0].description }}
          </p>
          <img
            class="w-[150px] h-auto"
            :src="
              `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`
            "
            alt=""
          />
        </div>
      </div>
    </div>
</template>

<script setup>

import axios from 'axios';
import { useRoute } from 'vue-router';

const route = useRoute();

const getWeatherData = async () => {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=f8a112765cebba30407ba978e721c85e&units=imperial`);
    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.dt * 1000 + localOffset;
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;
    console.log(weatherData.data);
    return weatherData.data;

    } catch (error) {
        console.log(error);
    }
};

const weatherData = await getWeatherData();

</script>