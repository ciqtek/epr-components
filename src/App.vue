<template>
  <p>{{value1}}</p>
  <eprSliderButton :min='0' :max="100" :step="[1, '5%', '10%']" v-model="value1" @change="slider1BtnChange" />
  <p>{{value2}}</p>
  <eprSliderButton :min='40' :max="250" :step="[1, '5%', '10%']" v-model="value2" @change="slider2BtnChange" />
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { eprSliderButton } from './components/'

export default defineComponent({
  name: 'App',
  components: {
    eprSliderButton
  },
  setup () {
    const value1 = ref(20)
    const value2 = ref(100)

    function slider1BtnChange (e: number) {
      value1.value = e
    }
    function slider2BtnChange (e: number) {
      value2.value = e
    }

    return {
      value1,
      value2,
      slider1BtnChange,
      slider2BtnChange
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
