<template>
  <div class="epr-btn">
    <span class="epr-btn-reduce" @click="handleChange('left')">
      <span class="math-minus"></span>
    </span>
    <span class="epr-btn-content">
      <input class="epr-btn-input" v-model="value" type="text" @change="changeEvent" @keydown.down="keyEvent('down')" @keydown.up="keyEvent('up')">
    </span>
    <span class="epr-btn-increase" @click="handleChange('right')">
      <span class="math-plus"></span>
    </span>
  </div>
</template>
<script lang="ts">
import { error } from 'console'
import { defineComponent, onMounted, ref } from 'vue'
export default defineComponent({
  name: 'epr-slider-input',
  props: {
    modelValue: {
      type: Number,
      default: 0
    },
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    step: {
      default: [1, 1]
    },
    precision: {
      type: Number,
      default: 2
    }
  },
  emits: ['change'],
  setup (props, { emit }) {
    const value = ref(0)

    onMounted(() => {
      initAndCheck()
    })

    // 初始化，并检查值
    function initAndCheck () {
      if (props.modelValue < props.min || props.modelValue > props.max) {
        return console.error('[EPR SLIDER INPUT]: 当前值不能小于最小值或大于最大值')
      } else {
        value.value = props.modelValue
      }
    }

    // 精度
    function toPrecision (n: number) {
      const p = props.precision
      const numReg = new RegExp(`^(.*\\..{${p}}).*$`)
      const num = n.toString().replace(numReg, '$1')
      value.value = +num
      return +num
    }

    // 鼠标 加减按钮 事件
    function handleChange (pos: string) {
      if (pos === 'left') {
        if (value.value - props.step[0] >= props.min) {
          value.value -= props.step[0]
        } else {
          value.value = props.min
        }
      }
      if (pos === 'right') {
        console.log(typeof value.value, typeof props.step[0], value.value, props.step[0], props.precision)
        if (value.value + props.step[0] <= props.max) {
          value.value -= -props.step[0]
        } else {
          value.value = props.max
        }
      }
      emit('change', toPrecision(value.value))
    }

    // 用户 手动输入 事件
    function changeEvent (e: Event) {
      const target = e.target as HTMLInputElement
      const n = target.value
      const m = isNaN(Number(n))
      if (m) {
        value.value = 0
        const error = '[EPR SLIDER INPUT]: 输入的值错误，请检查后输入'
        emit('change', 0, error)
      } else {
        const num = Number(n)
        if (num > props.max || num < props.min) {
          const error = '[EPR SLIDER INPUT]: 输入的值不能小于最小值或大于最大值，请检查后输入'
          value.value = 0
          emit('change', 0, error)
        } else {
          value.value = num
          emit('change', toPrecision(num))
        }
      }
    }

    // 键盘 上下按键 事件
    function keyEvent (str: string) {
      if (props.step[1]) {
        if (str === 'down') {
          if (value.value - props.step[1] >= props.min) {
            value.value -= props.step[1]
          } else {
            value.value = props.min
          }
        }
        if (str === 'up') {
          if (value.value + props.step[1] <= props.max) {
            value.value += props.step[1]
          } else {
            value.value = props.max
          }
        }
        emit('change', toPrecision(value.value))
      }
    }

    return {
      value,
      keyEvent,
      changeEvent,
      handleChange
    }
  }
})
</script>
<style lang="scss">
.epr-btn-content{
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1;
  box-sizing: border-box;
  .epr-btn-input{
    border: none;
    outline: none;
    width: 100%;
    height: 100%;
    text-align: center;
  }
}
</style>