<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text"
        v-model="searchQuery"
        @input="getSearchResult"
        placeholder="Search for a city or a state"
        class="py-2 px-1 w-full bg-transparent border-b
        focus:border-weather-secondary focus:outline-none
        focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
          v-if="searchResult">
        <p v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p v-if="!searchError && searchResult.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li v-for=" result in searchResult" :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)">
            {{ result.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>

import {ref} from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResult = ref(null);
const searchError = ref(null);
const router = useRouter();

const previewCity = (searchResult) => {
  router.push({
    name: "cityView",
    params: {state: searchResult.state, city: searchResult.city},
    query: {
      lat: searchResult.lat,
      lng: searchResult.lon,
      preview: true,
    },
  });
};

const getSearchResult = () => {
    clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout(async () => {
      if (searchQuery.value !== "" ) {
        try {
            const result = await axios.get(
            `https://nominatim.openstreetmap.org/search?city=${searchQuery.value}&format=jsonv2&addressdetails=1`);
            let mapping_result = [];
            for (let i = 0; i < result.data.length; i++) {
                mapping_result.push({
                  "id": result.data[i].place_id, 
                  "place_name": result.data[i].display_name,
                  "lat": result.data[i].lat,
                  "lon": result.data[i].lon,
                  "state": result.data[i].address.state,
                  "city":  result.data[i].display_name.split(",")[0],
                  });
            }
            searchError.value = false;
            searchResult.value = mapping_result;
            return;
        } catch {
          searchError.value = true;
        }

      }
      searchResult.value = null;
    }, 300);
};



</script>
