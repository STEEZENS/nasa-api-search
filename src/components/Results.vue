<template>
  <section>

    <!-- ZOOM WINDOW -->
    <div id="Zoom-Window" @click="setZoomIndex(null)" v-show="zoomIndex !== null">

      <button
        v-show="hasZoomIndexBefore"
        class="__step-button __step-before"
        ref="stepBeforeButton"
        @click.stop="setZoomIndex(zoomIndex - 1)">
      </button>

      <div class="__content" v-if="zoomIndex !== null">
        <img class="__zoomed-image" :src="zoomResult.links[0].href" :alt="zoomResult.data[0].title">
        <div class="__details">
          <div class="__title" v-text="zoomResult.data[0].title"></div>
          <div class="__date" v-text="getFriendlyDate(zoomResult.data[0].date_created)"></div>
        </div>
      </div>

      <button
        v-show="hasZoomIndexAfter"
        class="__step-button __step-next"
        ref="stepNextButton"
        @click.stop="setZoomIndex(zoomIndex + 1)">
      </button>

    </div>

    <!-- SEARCHING -->
    <div id="Fetching" v-show="fetching"></div>

    <!-- RESULTS -->
    <div id="Results" v-show="!fetching && results.results.length">
      <a
        href="javascript:;"
        class="Result"
        :class="{ _active: (zoomIndex === index) }"
        v-for="(result, index) in results.results"
        :key="result.data[0].nasa_id"
        tabindex="0"
        @click="setZoomIndex(index)">

        <!-- image -->
        <div class="__image" :style="getBackgroundImage(result.links[0].href)"></div>
        <!-- <img class="__image" :src="result.links[0].href" :alt="result.data[0].title" /> -->

        <div class="__details">
          <!-- title -->
          <div class="__title" v-text="result.data[0].title"></div>

          <!-- date -->
          <div class="__date" v-text="getFriendlyDate(result.data[0].date_created)"></div>
        </div>

      </a>
    </div>

    <!-- NO RESULTS -->
    <div id="No-Results" v-show="!fetching && results.query && !results.results.length">No Results for "<i>{{ results.query }}</i>"</div>

  </section>
</template>

<script>
export default {
  name: 'Results',
  data () {
    return {
      zoomIndex: null
    }
  },
  props: {
    fetching: Boolean,
    results: Object
  },
  computed: {
    zoomResult () {
      return (this.zoomIndex !== null) ? this.results.results[this.zoomIndex] : null
    },
    hasZoomIndexBefore () {
      return this.zoomIndex > 0
    },
    hasZoomIndexAfter () {
      return this.zoomIndex < (this.results.results.length - 1)
    }
  },
  watch: {
    zoomIndex: function (val, prevVal) {
      if (prevVal === null && val !== null) {
        document.addEventListener('keyup', this.handleZoomWindowKeyNavigation)
        setTimeout(() => {
          this.$refs.stepNextButton.focus()
        }, 50)
      } else if (prevVal !== null && val === null) {
        document.removeEventListener('keyup', this.handleZoomWindowKeyNavigation)
        document.getElementsByClassName('Result')[prevVal].focus()
      }
      if (val) {
        setTimeout(() => {
          window.scrollTo(0, (document.querySelector('.Result._active').offsetTop + 100))
        }, 50)
      }
    }
  },
  methods: {
    getBackgroundImage (img) {
      return `background-image: url('${img}');`
    },
    getFriendlyDate (date) {
      return date.substring(0, 10)
    },
    setZoomIndex (index) {
      this.zoomIndex = index
    },
    handleZoomWindowKeyNavigation (e) {
      const close = e.which === 27
      const before = e.which === 37
      const next = e.which === 39

      if (close) {
        return this.setZoomIndex(null)
      } else if (before && this.hasZoomIndexBefore) {
        this.setZoomIndex(this.zoomIndex - 1)
        this.$refs.stepButton.focus()
      } else if (next && this.hasZoomIndexAfter) {
        this.setZoomIndex(this.zoomIndex + 1)
        this.$refs.stepNextButton.focus()
      }
    }
  }
}
</script>

<style scoped lang="scss">
  #Zoom-Window {
    position: fixed;
    top: 0; left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 1;
    background-color: rgba(#0E0F11, .9);
    .__content {
      position: relative;
      top: 50%;
      transform:translateY(-50%);
      max-height: 100vh;
    }
    .__zoomed-image {
      max-height: 80vh;
      max-width: 80vw;
      box-shadow: 0 8px 20px -4px rgba(black, .2);
    }
  }
  .__step-button {
    position: absolute;
    z-index: 1;
    top: 0; bottom: 60px;
    margin: auto;
    width: 60px; height: 100px;
    cursor: pointer;
    background-color: transparent;
    border: none;
    &:before, &:after {
      content: "";
      display: block;
      position: absolute;
      left: 0; right: 0;
      margin: auto;
      width: 2px; height: 54%;
      background-color: white;
    }
  }
  .__step-button.__step-before {
    left: 3vw;
    &:before { top: 0; transform: rotateZ(30deg); }
    &:after { bottom: 0; transform: rotateZ(-30deg); }
  }
  .__step-button.__step-next {
    right: 3vw;
    &:before { top: 0; transform: rotateZ(-30deg); }
    &:after { bottom: 0; transform: rotateZ(30deg); }
  }

  #No-Results {
    position: relative;
    top: 25vh;
    color: white;
  }

  #Results {
    position: relative;
    margin: 0 auto;
    max-width: 1400px;
  }
  .Result {
    position: relative;
    display: inline-block;
    vertical-align: top;
    margin: 0 12px 40px 12px;
    width: 25%;
    overflow: hidden;
    border-radius: 6px;
    .__image {
      background-position: center;
      background-size: cover;
      background-repeat: no-repeat;
      width: 100%;
      height: 300px;
      border-radius: 6px;
      box-shadow: 0 4px 20px -3px rgba(black, .15);
      cursor: -moz-zoom-in;
      cursor: -webkit-zoom-in;
      cursor: zoom-in;
    }
    &._active {
      border: 2px solid white;
    }
  }
  .__details {
    padding: 24px;
  }
  .__title {
    color: white;
    font-size: 18px;
    font-weight: 600;
    padding-bottom: 12px;
  }
  .__date {
    color: rgba(white, .5);
    font-size: 14px;
    font-weight: 400;
  }

  #Fetching {
    $size: 50px;
    $border: 4px;
    $buffer: 10px;
    $color: #FFF;
    position: relative;
    top: 25vh;
    margin: 0 auto;
    height: $size;
    width: $size;
    transform: rotate(-45deg);
    &:before {
      content: "";
      display: block;
      position: absolute;
      top: 0;
      right: 0;
      left: 0;
      bottom: 0;
      margin: auto;
      width: $size - ($border * 2);
      height: $size - ($border * 2);
      border: $border solid rgba($color, .2);
      border-top: $border solid $color;
      border-radius: 50%;
      transition: all .2s ease 0s;
      animation: spin .7s cubic-bezier(0.560, 0.110, 0.220, 0.865) 0s infinite;
    }
    // animation keyframes
    @keyframes spin {
      from  { transform: rotate(45deg); }
      to    { transform: rotate(405deg); }
    }
  }
</style>
