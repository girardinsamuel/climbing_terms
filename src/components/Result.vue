<template>
  <div class="bg-white rounded p-4" :class="first && 'border-2 border-red-500 border-dashed'">
    <h1 class="font-medium text-lg mb-2">{{ result.title }}</h1>
    <div class="flex items-center space-x-4">
      <span class="text-gray-500 text-base" v-for="(trans, lang) in result.translations" :key="lang">
        {{ lang.toUpperCase() }} : {{ trans }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue'

defineProps({
  result: Object,
  first: Boolean,
  lang: String
})
</script>
