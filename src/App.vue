<template>
  <h1>EPR Components</h1>
  <div class="box">
    <div class="title">EPR Slider Button</div>
    <div class="value">min: {{min}} value: {{value}} max: {{max}} step: {{step}} precision: {{precision}}</div>
    <div class="item">
      <epr-slider-button :min='min' :max="max" :step="step" v-model="value" :precision="precision" @change="sliderBtnChange" />
    </div>
  </div>
  <div class="box">
    <div class="title">EPR Slider Input</div>
    <div class="value">min: {{min}} value: {{value2}} max: {{max}} step: {{step2}} precision: {{precision}}</div>
    <div class="item">
      <epr-slider-input :min='min' :max="max" :step="step2" v-model="value2" :precision="precision" @change="sliderInputChange" />
    </div>
  </div>
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
      step: [1, 2, '3', '4%'],
      step2: [1, 2],
      value: 50,
      value2: 50,
      precision: 2
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
.box{
  border: 1px solid #efefef;
  padding: 20px;
  display: flex;
  width: 600px;
  margin: 10px auto;
  flex-direction: column;
  justify-content: center;
}
.title, .value{
  margin-bottom: 10px;
}
.title{
  text-align: left;
}
.item{
  margin: 0 auto;
  width: 256px;
  height: 26px;
}
</style>
