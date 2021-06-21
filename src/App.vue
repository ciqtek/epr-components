<template>
  <epr-slider-button :min='min' :max="max" :step="step" v-model="value" :precision="precision" @change="sliderBtnChange" />
  <br />
  <epr-slider-input :min='min' :max="max" :step="step2" v-model="value2" :precision="precision" @change="sliderInputChange" />
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from 'vue'
import { eprSliderButton, eprSliderInput } from './components/'
interface Config {
  min: number
  max: number
  step: (string | number)[]
  step2: number[]
  value: number
  value2: number
  precision: number
}

export default defineComponent({
  name: 'App',
  components: {
    eprSliderButton,
    eprSliderInput
  },
  setup () {
    const config: Config = reactive({
      min: 0,
      max: 100,
      step: [0.211, '2', '3%'],
      step2: [0.001, 2],
      value: 50,
      value2: 30,
      precision: 3
    })

    function sliderBtnChange (e: number) {
      config.value = e
    }

    function sliderInputChange (e: number, error?: string) {
      config.value2 = e
      if (error) {
        console.log(error)
      }
    }

    return {
      ...toRefs(config),
      sliderBtnChange,
      sliderInputChange
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
