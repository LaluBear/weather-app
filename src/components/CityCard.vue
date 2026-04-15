<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-e-4xl shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <div class="text-3xl">{{ city.city }}</div>
            <div>{{ city.state }}</div>
        </div>
        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.current.temp) }}&deg;
            </p>
            <div class="flex gap-2">
                <span class="text-xs">H: {{ Math.round(city.weather.current.temp) }}&deg;</span>
                <span class="text-xs">L: {{ Math.round(city.weather.current.temp) }}&deg;</span>
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