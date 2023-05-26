<template>
  <main class="container text-white">
    <div class="relative pt-4 mb-8">
      <input 
      type="text" 
      @input="getSearchResults"
      v-model="searchQuery"
      placeholder="search for a city or state" 
      class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"/>
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <p v-if="searchError">Oops. Sorry! Something went wrong</p>
        <p v-if="mapboxSearchResults.length === 0">
          No results match your query. Try something else.
        </p>
        <template v-else>
          <li 
          v-for="searchResult in mapboxSearchResults" 
          :key="searchResult.id" 
          class="py-2 cursor-pointer"
          @click="previewCity(searchResult)"
          >
          {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const searchQuery = ref("")
const queryTimeOut = ref(null)
const mapboxAPIKey =  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const router = useRouter();
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(',');
  router.push({
    name: 'cityView',
    params: {
      state: state.replaceAll(" ", ""),
      city: city
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

const getSearchResults = () => {
  clearTimeout(queryTimeOut.value);
  queryTimeOut.value = setTimeout( async ()=> {
    if(searchQuery.value !== "") {
      try {
        const result = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
      );

      mapboxSearchResults.value = result.data.features;
      }
      catch {
        searchError.value = true
      }
      return;
    }
    mapboxSearchResults.value = null;
  }, 300)
}


</script>
