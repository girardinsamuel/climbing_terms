<template>
  <div class="flex flex-col min-h-screen pt-10 p-4 bg-yellow-400 items-center">
    <!-- Lang selector -->
    <nav class="flex space-x-4 mb-4" aria-label="Langs">
      <!-- Current: "bg-indigo-100 text-indigo-700", Default: "text-gray-500 hover:text-gray-700" -->
      <a @click="selectLang('en')"
        class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
        :class="lang == 'en' ? 'bg-yellow-100 text-yellow-700' : 'text-black hover:bg-yellow-100 hover:text-yellow-700'"
      >
        English
      </a>
      <a
        @click="selectLang('it')"
        class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
        :class="lang == 'it' ? 'bg-yellow-100 text-yellow-700' : 'text-black hover:bg-yellow-100 hover:text-yellow-700'"
      >
        Italian
      </a>
      <a
        @click="selectLang('fr')"
        class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
        :class="lang == 'fr' ? 'bg-yellow-100 text-yellow-700' : 'text-black hover:bg-yellow-100 hover:text-yellow-700'"
      >
        French
      </a>
    </nav>
    <!-- Search input -->
    <div class="w-full sm:w-1/3 mx-auto">
      <div class="mt-1 relative rounded-md shadow-sm">
        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
          </svg>
        </div>
        <input @input="search" type="text" name="search" id="search"
        class="focus:ring-indigo-500 focus:border-indigo-500 block w-full pl-10 sm:text-sm border-gray-300 rounded-md" placeholder="Search climbing term ..." autocomplete="off">
      </div>
    </div>
    <ul class="mt-10 px-0 sm:px-10 w-full">
      <li v-for="(result, index) in results" :key="result.refIndex" class="mb-2">
        <result :result="displayResult(result)" :first="index == 0" :lang="lang"/>
      </li>
    </ul>
  </div>
</template>

<script>
import dictionary from "./data/dictionary"
import Fuse from 'fuse.js'
import Result from './components/Result.vue'

export default {
  name: "App",
  components: {
    Result,
  },
  data () {
    return {
      fuse: null,
      dictionary,
      results: [],
      lang: null,
    }
  },
  created () {
    const options = {
      includeScore: false,
      keys: ['word', 'translations.it', 'translations.fr', 'translations.de']
    }

    this.fuse = new Fuse(dictionary, options)

    this.lang = localStorage.selectedLang || 'en'
  },
  methods: {
    search (event) {
      const results = this.fuse.search(event.target.value)
      this.results = results.slice(0, 8)
    },
    selectLang (lang) {
      this.lang = lang
      localStorage.selectedLang = lang;
    },
    displayResult (fuseResult) {
      const result = fuseResult.item
      const translations = {...result.translations, en: result.word }
      delete translations[this.lang]
      return {
        title: result.translations[this.lang] || result.word,
        translations
      }
    }
  },
}
</script>
