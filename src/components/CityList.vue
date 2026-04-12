<template>
    <div 
    v-for="city in savedCities" :key="city.id?city.id:0">
        <CityCard :city="city" 
        @click="goToCityView(city)"/>
    </div>
</template>

<script setup lang="ts">
import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';
import type { locationObjType } from '@/type/OpenWeatherOneCall';

console.log(import.meta)
const savedCities = ref<locationObjType[]>([]);
const openWeatherMapAPIKEY = import.meta.env.VITE_OPENWEATHERMAP_API_KEY
const getCities = async () => {
    if (localStorage.getItem('savedCities')){
        const data = localStorage.getItem("savedCities")?localStorage.getItem("savedCities"):"{}"
        savedCities.value = JSON.parse(
            data?data:"{}"
        )
    }
    const requests = savedCities.value.map((city)=>{
        return axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${city.coord.lat}&lon=${city.coord.lng}&appid=${openWeatherMapAPIKEY}`)
    });

    const weatherData = await Promise.all(requests);

    await new Promise((res) => setTimeout(res,1000)); 

    weatherData.forEach((value, index)=>{
        const city = savedCities.value[index];
        if (city) {
            city.weather = value.data;
        }
        console.log(index);
        console.log(value);
    })
}
const router = useRouter();
await getCities();

const goToCityView = (city: locationObjType) =>{
    router.push({
        name:"cityView",
        params: { state: city.state, city: city.city },
        query: {id: city.id,lat: city.coord.lat, lng:city.coord.lng}
    })
}
</script>

<style lang="scss" scoped>

</style>