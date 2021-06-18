<template>
  <p>value: {{value}}</p>
  <p>min: {{min}}</p>
  <p>max: {{max}}</p>
  <p>step: {{step}}</p>
  <epr-slider-button :min='min' :max="max" :step="step" v-model="value" @change="slider1BtnChange" />
</template>

<script lang="ts">
import { defineComponent, reactive, ref, toRefs } from 'vue'
import { eprSliderButton } from './components/'

export default defineComponent({
  name: 'App',
  components: {
    eprSliderButton
  },
  setup () {
    const value1 = ref(20)
    const value2 = ref(100)
    const config = reactive({
      min: 20,
      max: 320,
      step: ['1', '1%', '3%'],
      value: 20
    })

    function slider1BtnChange (e: number) {
      config.value = e
    }

    return {
      value1,
      value2,
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
