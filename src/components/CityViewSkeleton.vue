<template>
    <div class="flex flex-col flex-1 items-center text-white">
        <!-- Weather Overview -->
         <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">
                <p class="text-sm mb-12">
                </p>
                <p class="text-8xl mb-8">
                </p>
                <p>
                </p>
                <p class="capitalize">
                </p>
                <img
                    class="w-{150px} h-auto"
                    :src="`https://openweathermap.org/payload/api/media/file/${weatherData.data.current.weather[0].icon}.png`"
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
                    v-for="hourData in weatherData.data.hourly"
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
                v-for="day in weatherData.data.daily"
                :key="day.dt"
                class="flex items-center"
                ><p class="flex-1">
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

<script setup>

</script>
