<template>
  <epr-slider-button :min='min' :max="max" :step="step" v-model="value" :precision="precision" @change="slider1BtnChange" />
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from 'vue'
import { eprSliderButton } from './components/'
interface Config {
  min: number
  max: number
  step: (string | number)[]
  value: number
  precision: number
}

export default defineComponent({
  name: 'App',
  components: {
    eprSliderButton
  },
  setup () {
    const config: Config = reactive({
      min: 0,
      max: 100,
      step: [1, '2', '3%'],
      value: 50,
      precision: 2
    })

    function slider1BtnChange (e: number) {
      config.value = e
    }

    return {
      ...toRefs(config),
      slider1BtnChange
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
