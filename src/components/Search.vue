<template>
  <form @submit.prevent="handleSearchSubmit(query)" :class="{ hasSubmitted: hasSubmitted }">
    <input
      id="Search"
      type="text"
      placeholder="Search the Cosmos..."
      autofocus
      autocomplete="off"
      v-model="query" />
  </form>
</template>

<script>
import Axios from 'axios'

export default {
  name: 'Search',
  data () {
    return {
      query: '',
      hasSubmitted: false
    }
  },
  methods: {
    handleSearchSubmit (query) {
      this.hasSubmitted = true
      this.$emit('fetchingResults', true)
      Axios.get(`https://images-api.nasa.gov/search?q=${this.query}&media_type=image`)
        .then((response) => {
          this.$emit('fetchingResults', false)
          this.$emit('updateSearchResults', {
            query: this.query,
            results: response.data.collection.items
          })
        })
    }
  }
}
</script>

<style scoped lang="scss">
  form {
    position: relative;
    top: 40vh;
    padding-top: 60px;
    padding-bottom: 80px;
    transition: top .3s ease 0s;
    &.hasSubmitted {
      top: 0;
    }
  }
  #Search {
    text-align: center;
    -webkit-appearance: none;
    border: none;
    border-bottom: 1px solid rgba(white, .5);
    background-color: transparent;
    font-size: 32px;
    line-height: 56px;
    color: white;
    width: 40%;
    font-weight: 600;
    transition: border .3s ease 0s;
    &:focus {
      outline: none;
      border-bottom-color: white;
    }
  }
</style>
