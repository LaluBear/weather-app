<template>
    <div class="flex py-6 px-4 bg-weather-secondary rounded-2xl shadow-md cursor-pointer transition-all duration-200 hover:scale-[1.02] hover:bg-weather-primary/80">
        <div class="flex flex-col flex-1">
            <div class="text-3xl font-bold">{{ city.city }}</div>
            <div class="text-sm opacity-70">{{ city.state }}</div>
        </div>
        <div class="flex flex-col gap-1 text-right">
            <p class="text-3xl font-semibold">
                {{ Math.round(city.weather.current.temp) }}&deg;
            </p>
            <div class="flex gap-2 text-xs opacity-70">
                <span>H: {{ Math.round(city.weather.current.temp) }}&deg;</span>
                <span>L: {{ Math.round(city.weather.current.temp) }}&deg;</span>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
defineProps({
    city:{
        type: Object,
        default:( )=>{},
    }
})
</script>

<style lang="scss" scoped>

</style>