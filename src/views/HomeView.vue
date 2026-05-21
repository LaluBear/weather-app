

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <div class="relative flex items-center">
        <i class="fas fa-search absolute left-4 text-white/50"></i>
        <input type="text"
        placeholder="Search for a city or state"
        v-model="searchQuery"
        @input="getSearchResults"
        class="py-2 pl-11 pr-4 w-full bg-transparent border-b
        border-white/20 focus:border-weather-secondary focus:outline-none
        transition-colors duration-200 focus:shadow-[0px_1px_0_0_#004E71]">
      </div>
      <ul class="absolute bg-weather-secondary text-white
      w-full shadow-xl py-2 px-4 top-full left-0 z-10 rounded-b-lg overflow-hidden">
        <p v-if="searchError" class="py-2 px-4 text-sm text-red-300">
          Sorry, something went wrong, please try again
        </p>
        <p v-if="mapboxSearchResults && searchQuery && mapboxSearchResults.length === 0" class="py-2 px-4 text-sm opacity-70">
          No search results
        </p>
        <li
          v-for="searchResult in mapboxSearchResults"
          :key="searchResult.id"
          class="py-2 px-4 cursor-pointer hover:bg-white/10 transition-colors duration-150 text-sm"
          v-if="mapboxSearchResults"
          @click="previewCity(searchResult)"
        >
        {{ searchResult.properties.place_formatted }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <template #default>
          <CityList />
        </template>
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>
<script setup lang="ts">

import CityCardSkeleton from '@/components/CityCardSkeleton.vue';
import CityList from '@/components/CityList.vue';
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';

interface MapboxFeature {
  id: string;
  properties: {
    place_formatted: string;
  };
  geometry: {
    coordinates: [number, number];
  };
}

const router = useRouter();
const mapBoxAPIKEY = import.meta.env.VITE_MAPBOX_API_KEY;

const searchQuery = ref("");
const searchError = ref(false)
const queryTimeout = ref<number | undefined>(undefined);
const mapboxSearchResults = ref<MapboxFeature[]>([]);

const previewCity = (searchResult:any) => {
  const [city, state] = searchResult.properties.place_formatted.split(",")
  
  router.push({
    name: "cityView",
    params: {state: state.replaceAll(" ",""), city: city},
    query:{
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: "true"
    }
  })
}
const getSearchResults = ()=>{
  clearTimeout(queryTimeout.value)
  searchError.value = false;
  queryTimeout.value = setTimeout(async ()=>{
    if(searchQuery.value !== ""){

      try{

        const result = await axios.get(
          `https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}.json&access_token=${mapBoxAPIKEY}&types=place`
        )
        
        mapboxSearchResults.value = result.data.features
      } catch{
        searchError.value = true
      }

      return;
    }
    mapboxSearchResults.value = [];
    return;
  }, 300)
}
</script>