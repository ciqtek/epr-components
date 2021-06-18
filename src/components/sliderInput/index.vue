<template>
  <div>slider input</div>
</template>
<script lang="ts">
import { defineComponent, onMounted } from 'vue'
export default defineComponent({
  name: 'epr-slider-input',
  setup () {
    onMounted(() => {
      console.log('epr slider input')
    })
  }
})
</script>