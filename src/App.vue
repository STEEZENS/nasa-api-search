<template>
  <div id="app">
    <Search
      @fetchingResults="isFetching"
      @updateSearchResults="updateSearchResults" />
    <Results
      :fetching="fetching"
      :results="results" />
  </div>
</template>

<script>
import Search from './components/Search.vue'
import Results from './components/Results.vue'

export default {
  name: 'app',
  components: {
    Search,
    Results
  },
  data () {
    return {
      fetching: false,
      results: {
        query: '',
        results: []
      }
    }
  },
  methods: {
    updateSearchResults (results) {
      this.results = results
    },
    isFetching (fetching) {
      this.fetching = fetching
    }
  }
}
</script>

<style lang="scss">
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  body {
    background-color: #0E0F11; //#080808;
    font-family: 'Proxima Nova', 'Open Sans', 'Helvetica', sans-serif;
  }
  img {
    max-width: 100%;
  }
  a {
    text-decoration: none;
  }
  #app {
    text-align: center;
  }
</style>
