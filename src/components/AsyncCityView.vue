<template>
    <div class="flex flex-col flex-1 items-center text-white">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4
        bg-weather-secondary w-full text-center">
        <p>
            Nothing Only Banner
        </p>
        </div>
        <!-- Weather Overview -->
         <div class="flex flex-col items-center text-white py-12">
            
            <h1 class="text-4xl mb-2">
                <p class="text-sm mb-12">
                    {{ 
                        new Date(weatherData?.data.currentTime).toLocaleDateString(
                            "en-us",
                            {
                                weekday: "short",
                                day: "2-digit",
                                month: "long",

                            }
                        )    
                    }}
                    {{ 
                        new Date(weatherData?.data.currentTime).toLocaleTimeString(
                            "en-us",
                            {
                                timeStyle: "short",
                            }
                        )
                    }}
                </p>
                <p class="text-8xl mb-8">
                    {{ Math.round(weatherData?.data.current.temp) }}&deg;
                </p>
                <p>
                    Feels like
                    {{ Math.round(weatherData?.data.current.feels_like) }}&deg;
                </p>
                <p class="capitalize">
                    {{ weatherData?.data.current.weather[0].description }}
                </p>
                <img
                    class="w-{150px} h-auto"
                    :src="`https://openweathermap.org/payload/api/media/file/${weatherData?.data.current.weather[0].icon}.png`"
                />
            </h1>
        
         </div>
         <hr class="border-white/10 text-white py-12"/>
         <!-- Hourly Weather -->
         <div class="max-w-screen-md w-full py-12">
             <div class="mx-8 text-white">
                 <h2 class="mb-4">Hourly Weather</h2>
                 <div class="flex gap-10 overflow-x-scroll">
                    <div 
                    v-for="hourData in weatherData?.data.hourly"
                    :key="hourData.dt"
                    class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md">
                        {{ new Date(
                            hourData.currentTime).toLocaleTimeString(
                                "en-us",
                                    {hour: "numeric"}
                            ) }}
                        </p>
                        <img 
                        class="w-auto h-{50p} object-cover" 
                        :src="`https://openweathermap.org/payload/api/media/file/${hourData.weather[0].icon}.png`"
                        />
                        <p class="">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
            
        </div>
        <hr class="border-white/10 text-white py-12"/>
        <!-- Weakly Weather -->
         <div class="max-w-screen-md w-full py-12">
            <div class="">7 Day Forecast</div>
            <div
                v-for="day in weatherData?.data.daily"
                :key="day.dt"
                class="flex items-center"
                ><p class="flex-1">

                    {{ new Date(day.dt * 1000).toLocaleDateString(
                        "en-us",
                        {weekday:"long"}
                    ) }}
                </p>
                <img
                class="w-{150px} h-auto"
                :src="`https://openweathermap.org/payload/api/media/file/${day.weather[0].icon}.png`"
                />
                <div class="flex flex-1 gap-4 justify-end">
                    
                    <p>H: {{ Math.round(day.temp.max) }}</p>
                    <p>L: {{ Math.round(day.temp.min) }}</p>
                </div>
            </div>
         </div>
         <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
         @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
         </div>

    </div>
</template>

<script setup lang="ts">
import type { CurrentWeather, Hourly, locationObjType } from '@/type/OpenWeatherOneCall';
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const openWeatherMapAPIKEY = import.meta.env.VITE_OPENWEATHERMAP_API_KEY
const route = useRoute();
const router = useRouter();
const getWeatherData = async () => {
    try
    {
        const weatherData= await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${openWeatherMapAPIKEY}`);
        
        //cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData?.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        weatherData.data.hourly.forEach((hour: Hourly) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        });
        return weatherData;
    } catch(err){
        console.log(err);
    }
}

const weatherData = await getWeatherData();
const removeCity = () => {
    const data = localStorage.getItem('savedCities');
    const cities = JSON.parse(data?data:"{}");
    const updatedCities = cities.filter((city: locationObjType)=>{city.id!==route.query.id})
    localStorage.setItem("savedCities",JSON.stringify(updatedCities))
    router.push({name:"home"})
}
</script>

<style lang="scss" scoped>

</style>