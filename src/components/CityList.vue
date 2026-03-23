<template>
    <div 
    v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" 
        @click="goToCityView(city)"/>
    </div>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';

console.log(import.meta)
const savedCities = ref([]);
const openWeatherMapAPIKEY = import.meta.env.VITE_OPENWEATHERMAP_API_KEY
const getCities = async () => {
    if (localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(
            localStorage.getItem("savedCities")
        )
    }
    const requests = []
    savedCities.value.forEach((city)=>{
        requests.push(
           axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${city.coord.lat}&lon=${city.coord.lng}&appid=${openWeatherMapAPIKEY}`)
        )
    });

    const weatherData = await Promise.all(requests);

    await new Promise((res) => setTimeout(res,1000)); 

    weatherData.forEach((value, index)=>{
        savedCities.value[index].weather = value.data;
        console.log(index);
        console.log(value);
    })
}
const router = useRouter();
await getCities();

const goToCityView = (city) =>{
    router.push({
        name:"cityView",
        params: { state: city.state, city: city.city },
        query: {id: city.id,lat: city.coord.lat, lng:city.coord.lng}
    })
}
</script>

<style lang="scss" scoped>

</style>