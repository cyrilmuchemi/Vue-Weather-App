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
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const searchQuery = ref("")
const queryTimeOut = ref(null)
const mapboxAPIKey =  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const mapboxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeOut.value);
  queryTimeOut.value = setTimeout( async ()=> {
    if(searchQuery.value !== "") {
      const result = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
      );

      mapboxSearchResults.value = result.data.features;
      console.log(mapboxSearchResults.value)
      return;
    }
    mapboxSearchResults.value = null;
  }, 300)
}


</script>
