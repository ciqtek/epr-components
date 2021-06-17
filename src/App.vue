<template>
  <slider-btn :min='50' :max="250" :step="[10, '6%', 20]" v-model="value" @change="sliderBtnChange" />
</template>

<script lang="ts">
import { defineComponent, reactive, ref } from 'vue'
import SliderBtn from './components/SliderBtn.vue'

export default defineComponent({
  name: 'App',
  components: {
    SliderBtn
  },
  setup () {
    const value = ref(20)
    const obj = reactive({
      num: 1
    })

    function sliderBtnChange (e: number) {
      console.log('app.vue e', e)
    }

    return {
      value,
      obj,
      sliderBtnChange
    }
  }
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
