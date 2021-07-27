<template>
  <div
    class="flex flex-col min-h-screen pt-10 p-4 items-center justify-between"
    :class="mode == 'dark' ? 'bg-pattern-dark' : 'bg-pattern'"
  >
    <div class="w-full">
      <!-- Lang selector -->
      <nav class="flex space-x-4 mb-4 justify-center" aria-label="Langs">
        <!-- Current: "bg-indigo-100 text-indigo-700", Default: "text-gray-500 hover:text-gray-700" -->
        <a @click="selectLang('en')"
          class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
          :class="lang == 'en' ? 'bg-yellow-100 text-yellow-700' : 'dark:text-white text-black hover:bg-yellow-100 hover:text-yellow-700 dark:hover:text-yellow-700'"
        >
          English
        </a>
        <a
          @click="selectLang('it')"
          class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
          :class="lang == 'it' ? 'bg-yellow-100 text-yellow-700' : 'dark:text-white text-black hover:bg-yellow-100 hover:text-yellow-700 dark:hover:text-yellow-700'"
        >
          Italian
        </a>
        <a
          @click="selectLang('fr')"
          class="px-3 py-2 font-medium text-sm rounded-md cursor-pointer"
          :class="lang == 'fr' ? 'bg-yellow-100 text-yellow-700' : 'dark:text-white text-black hover:bg-yellow-100 hover:text-yellow-700 dark:hover:text-yellow-700'"
        >
          French
        </a>
      </nav>

      <!-- Search input -->
      <div class="w-full sm:w-2/3 lg:w-1/3 mx-auto">
        <div class="mt-1 relative rounded-md shadow-sm">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
            </svg>
          </div>
          <input @input="search" v-model="query" type="text" name="search" id="search"
          class="dark:bg-gray-600 dark:text-white text-black bg-white focus:ring-yellow-700 focus:border-yellow-700 block w-full pl-10 sm:text-sm border-yellow-500 rounded-md" placeholder="Search climbing term ..." autocomplete="off">
        </div>
      </div>
      <!-- Results -->
      <ul class="mt-10 px-0 sm:px-10 w-full z-10">
        <li v-for="result in results" :key="result.title" class="mb-2">
          <result :result="result" />
        </li>
      </ul>
      <div class="text-center" v-if="query != '' && results.length == 0">
        <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 dark:text-white text-yellow-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M18.364 18.364A9 9 0 005.636 5.636m12.728 12.728A9 9 0 015.636 5.636m12.728 12.728L5.636 5.636" />
        </svg>
        <h3 class="mt-2 text-sm font-medium dark:text-white text-gray-900">No translations found !</h3>
        <p class="mt-1 text-sm dark:text-gray-200 text-yellow-700">
          Soon you will be able to report missing words
        </p>
        <div class="mt-6">
          <button type="button" @click="report" class="inline-flex items-center px-4 py-2 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-yellow-600 hover:bg-yellow-700">
            <!-- Heroicon name: solid/plus -->
            <svg class="-ml-1 mr-2 h-5 w-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
              <path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" />
            </svg>
            Report missing words
          </button>
        </div>
      </div>
      <!-- Eventual search history -->
      <div v-if="history.length > 0" class="my-10 w-full px-0 sm:px-10 z-10">
        <div class="flex items-center justify-between">
          <h2 class="dark:text-white text-black text-md text-left mb-2 font-medium">Recent searchs</h2>
          <a @click="clearHistory" class="dark:text-yellow-200 text-yellow-700 cursor-pointer underline dark:hover:text-yellow-600 hover:text-yellow-900">clear</a>
        </div>
        <ul class="w-full z-10">
          <li v-for="result in history" :key="result.title" class="mb-2">
            <result :result="result" class="bg-gray-100" />
          </li>
        </ul>
      </div>
    </div>

    <!-- Footer -->
    <div class="bottom-4 inset-x-0 text-center text-sm flex justify-center items-center space-x-2 dark:text-white text-black">
      <span>Crafted by <a href="https://twitter.com/girardin_sam" class="dark:text-yellow-300 text-yellow-700 underline dark:hover:text-yellow-600 hover:text-yellow-900">Samuel Girardin</a></span>
      <a href="https://github.com/girardinsamuel/climbing_terms"><svg width="24" height="24" fill="currentColor" class="dark:text-yellow-200 text-yellow-700 dark:hover:text-yellow-600 hover:text-yellow-900"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.463 2 11.97c0 4.404 2.865 8.14 6.839 9.458.5.092.682-.216.682-.48 0-.236-.008-.864-.013-1.695-2.782.602-3.369-1.337-3.369-1.337-.454-1.151-1.11-1.458-1.11-1.458-.908-.618.069-.606.069-.606 1.003.07 1.531 1.027 1.531 1.027.892 1.524 2.341 1.084 2.91.828.092-.643.35-1.083.636-1.332-2.22-.251-4.555-1.107-4.555-4.927 0-1.088.39-1.979 1.029-2.675-.103-.252-.446-1.266.098-2.638 0 0 .84-.268 2.75 1.022A9.606 9.606 0 0112 6.82c.85.004 1.705.114 2.504.336 1.909-1.29 2.747-1.022 2.747-1.022.546 1.372.202 2.386.1 2.638.64.696 1.028 1.587 1.028 2.675 0 3.83-2.339 4.673-4.566 4.92.359.307.678.915.678 1.846 0 1.332-.012 2.407-.012 2.734 0 .267.18.577.688.48C19.137 20.107 22 16.373 22 11.969 22 6.463 17.522 2 12 2z"></path></svg></a>
      <span @click="toggleMode">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" class="dark:text-yellow-300 text-yellow-700 dark:hover:text-yellow-600 hover:text-yellow-900 cursor-pointer" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
        </svg>
      </span>
      <a @click="report" class="dark:text-yellow-300 text-yellow-700 dark:hover:text-yellow-600 hover:text-yellow-900 cursor-pointer underline">
        Missing translations ?
      </a>
    </div>
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
      history: [],
      lang: null,
      mode: null,
      query: "",
    }
  },
  created () {
    const options = {
      includeScore: false,
      keys: ['word', 'translations.it', 'translations.fr', 'translations.de']
    }

    this.fuse = new Fuse(dictionary, options)
    // load from storage
    this.lang = localStorage.selectedLang || 'en'
    this.history = JSON.parse(localStorage.history || "[]") || []
    if (localStorage.theme === 'dark' || (!('theme' in window.localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
      document.documentElement.classList.add('dark')
      localStorage.theme = 'dark'
      this.mode = "dark"
    } else {
      document.documentElement.classList.remove('dark')
      localStorage.theme = 'light'
      this.mode = "light"
    }
  },
  beforeMount() {
    window.addEventListener("beforeunload", this.saveDataInHistory)
  },
  methods: {
    search () {
      if (this.query == "") {
        this.results = []
        return
      }
      const results = this.fuse.search(this.query)
      // display only 8 results
      this.results = results.slice(0, 8).map(r => {
        const result = r.item
        const translations = {...result.translations, en: result.word }
        delete translations[this.lang]
        const title = result.translations[this.lang] || result.word
        return {
          title,
          translations,
          exact: this.query == title
        }
      })

      // save in history last result
      const res = this.results[0]
      if (this.history.filter(r => res.title === r.title).length == 0) {
        this.history.unshift(res)
      }

      // keep only 5 items in history
      this.history.splice(5)
    },
    selectLang (lang) {
      this.lang = lang
      localStorage.selectedLang = lang;
      // refresh search
      this.search()
    },
    clearHistory () {
      this.history = []
      localStorage.history = []
    },
    saveDataInHistory() {
      localStorage.history = JSON.stringify(this.history)
    },
    toggleMode () {
      if (this.mode == "dark") {
        this.mode = "light"
        document.documentElement.classList.remove('dark')
        localStorage.theme = "light"
      } else {
        this.mode = "dark"
        document.documentElement.classList.add('dark')
        localStorage.theme = "dark"
      }
    },
    report () {
      window.open(
        "https://github.com/girardinsamuel/climbing_terms/issues/new?template=add-missing-words.md&title=Add+new+translations",
        "_blank"
      )
    }
  },
}
</script>
