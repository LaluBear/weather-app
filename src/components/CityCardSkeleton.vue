<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md">
        <div class="flex flex-col flex-1 gap-2">
            <AnimatedPlaceHolder class="max-w-[50%]"/>
            <AnimatedPlaceHolder class="max-w-[40%]"/>
        </div>
        <div class="flex flex-col flex-1 items-end">
            <AnimatedPlaceHolder class="max-w-[50px] w-full" />
            <AnimatedPlaceHolder class="max-w-[40px] w-full" />
        </div>
    </div>
</template>

<script setup lang="ts">
import AnimatedPlaceHolder from './AnimatedPlaceHolder.vue';

</script>