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

            <div class="text-center">
                <p class="text-4xl font-bold mb-2">
                    {{ route.params.city }}, {{ route.params.state }}
                </p>
                <p class="text-sm opacity-70 mb-12">
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
                <div class="flex flex-col items-center gap-4">
                    <p class="text-8xl font-bold">
                        {{ Math.round(weatherData?.data.current.temp) }}&deg;
                    </p>
                    <div class="flex flex-col items-center gap-1">
                        <p class="text-lg">
                            Feels like {{ Math.round(weatherData?.data.current.feels_like) }}&deg;
                        </p>
                        <p class="capitalize text-sm opacity-80">
                            {{ weatherData?.data.current.weather[0].description }}
                        </p>
                    </div>
                    <img
                        class="w-32 h-32 object-contain"
                        :src="`https://openweathermap.org/payload/api/media/file/${weatherData?.data.current.weather[0].icon}.png`"
                    />
                </div>
            </div>

         </div>
         <hr class="border-white/10 text-white py-12"/>
         <!-- Hourly Weather -->
         <div class="max-w-screen-md w-full py-12">
             <div class="mx-8 text-white">
                 <h2 class="text-xl font-semibold mb-6">Hourly Weather</h2>
                 <div class="flex gap-6 overflow-x-auto pb-4 scrollbar-hide">
                    <div
                    v-for="hourData in weatherData?.data.hourly"
                    :key="hourData.dt"
                    class="flex flex-col gap-3 items-center p-4 rounded-2xl bg-white/5 min-w-[100px] transition-colors hover:bg-white/10">
                        <p class="whitespace-nowrap text-sm font-medium">
                        {{ new Date(
                            hourData.currentTime).toLocaleTimeString(
                                "en-us",
                                    {hour: "numeric"}
                            ) }}
                        </p>
                        <img
                        class="w-12 h-12 object-cover"
                        :src="`https://openweathermap.org/payload/api/media/file/${hourData.weather[0].icon}.png`"
                        />
                        <p class="text-lg font-bold">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>

        </div>
        <hr class="border-white/10 text-white py-12"/>
        <!-- Weekly Weather -->
         <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="text-xl font-semibold mb-6">7 Day Forecast</h2>
                <div class="flex flex-col gap-3">
                    <div
                        v-for="day in weatherData?.data.daily"
                        :key="day.dt"
                        class="flex items-center justify-between p-4 rounded-2xl bg-white/5 transition-colors hover:bg-white/10">
                        <p class="flex-1 font-medium">
                            {{ new Date(day.dt * 1000).toLocaleDateString(
                                "en-us",
                                {weekday:"long"}
                            ) }}
                        </p>
                        <div class="flex items-center gap-6">
                            <img
                            class="w-12 h-12 object-contain"
                            :src="`https://openweathermap.org/payload/api/media/file/${day.weather[0].icon}.png`"
                            />
                            <div class="flex gap-4 min-w-[100px] justify-end text-sm">
                                <p class="font-bold">H: {{ Math.round(day.temp.max) }}&deg;</p>
                                <p class="opacity-70">L: {{ Math.round(day.temp.min) }}&deg;</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
         </div>
         <div class="flex items-center justify-center gap-2 py-12 text-white cursor-pointer duration-200 hover:text-red-400 group"
         @click="removeCity">
            <i class="fa-solid fa-trash text-lg group-hover:scale-110 transition-transform"></i>
            <p class="font-medium">Remove City</p>
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
        const weatherData= await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${openWeatherMapAPIKEY}&units=metric`);

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