<template>
  <div class="dark:bg-gray-600 bg-white rounded p-4" :class="result.exact && 'border-2 dark:border-yellow-500 border-green-600 border-dashed'">
    <h1 class="font-medium text-lg mb-2 dark:text-yellow-400 text-black">{{ result.title }}</h1>
    <div class="flex items-center space-x-4">
      <div class="dark:text-gray-100 text-gray-500 text-base flex items-center" v-for="(trans, lang) in result.translations" :key="lang">
        <img :src="`/flags/${lang}.png`" class="h-4 w-4 mr-2">
        <span>{{ trans }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue'

defineProps({
  result: Object,
})
</script>
