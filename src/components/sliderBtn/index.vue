<template>
  <div class="epr-btn">
    <span class="epr-btn-reduce" @click="handleChange('left')">
      <span class="math-minus"></span>
    </span>
    <span class="epr-btn-track" @mousedown.self="mousedownEvent" @mouseup.self="mouseupEvent" @mouseout="clearTimer" ref="track">
      <span class="epr-btn-slider" ref="slider" @mousedown="sliderMouseDown" @mouseup="sliderMouseUp" @mousemove="sliderMouseMove" @mouseout="sliderMouseOut"></span>
    </span>
    <span class="epr-btn-increase" @click="handleChange('right')">
      <span class="math-plus"></span>
    </span>
  </div>
</template>
<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'
export default defineComponent({
  name: 'epr-slider-button',
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
      type: Array,
      default: [1, '5%', '10%']
    },
    precision: {
      type: Number,
      default: 2
    }
  },
  emits: ['change'],
  setup (props, { emit }) {
    const slider = ref() // 滑块 dom
    const track = ref() // 滑道 dom
    let sliderWidth = 0 // 滑块的宽度
    let trackWidth = 0 // 滑道的宽度
    let timer1: any = 0 // 判断是点击还是长按
    let timer2: any = 0 // 长按时，间隔时间
    let canMove = false // 判断能否拖拽
    let x = 0 // 拖拽的位移距离

    // 初始化，并检查值
    function initAndCheck () {
      trackWidth = track.value.offsetWidth
      sliderWidth = slider.value.offsetWidth
      if (props.modelValue < props.min || props.modelValue > props.max) {
        return console.error('[EPR SLIDER BUTTON]: 当前值不能小于最小值或大于最大值')
      } else {
        const step =  props.modelValue * (trackWidth - sliderWidth) / (props.max - props.min)
        slider.value.style.left = step + 'px'
      }
    }
    // 获取当前 slider 位置
    function getSliderLeft () {
      const sLeft = window.getComputedStyle(slider.value).left
      return Number(sLeft.replace('px', ''))
    }

    // 精度
    function toPrecision (n: number) {
      const p = props.precision
      const numReg = new RegExp(`^(.*\\..{${p}}).*$`)
      const num = n.toString().replace(numReg, '$1')
      return +num
    }
    // 返回最新的值
    function returnValue () {
      const newPos = getSliderLeft()
      const value = (props.max - props.min) * newPos / (trackWidth - sliderWidth) + props.min
      emit('change', toPrecision(value))
    }
    // 两侧按钮
    function handleChange (pos: string) {
      const sPos = getSliderLeft()
      const propsStep = props.step[0]
      let step = 0
      if (typeof propsStep === 'string') {
        if (propsStep.includes('%')) {
          step = (trackWidth - sliderWidth) * Number(propsStep.replace('%', '')) / 100
        } else {
          step = Number(propsStep) * (trackWidth - sliderWidth) / (props.max - props.min)
        }
      } else if (typeof propsStep === 'number') {
        step = propsStep * (trackWidth - sliderWidth) / (props.max - props.min)
      } else {
        return console.error('[EPR SLIDER BUTTON]: 步进的值只能为：整数，数字型字符串 或 带百分号的字符串，如：[1, "2", "5%"]')
      }
      if (pos === 'left') {
        if (sPos - step < 0) {
          slider.value.style.left = '0px'
        } else {
          slider.value.style.left = (sPos - step) + 'px'
        }
      }
      if (pos === 'right') {
        if (sPos + sliderWidth + step < trackWidth) {
          slider.value.style.left = (sPos + step) + 'px'
        } else {
          slider.value.style.left = (trackWidth - sliderWidth) + 'px'
        }
      }
      returnValue()
    }
    // 鼠标按下事件
    function mousedownEvent (e: MouseEvent) {
      timer1 = setTimeout(() => {
        longpress(e)
      }, 300)
    }
    // 单击事件
    function mouseupEvent (e: MouseEvent) {
      clearTimeout(timer1)
      clearInterval(timer2)
      if (timer1 !== 0) {
        const offsetX = e.offsetX
        const sPos = getSliderLeft()
        const propsStep = props.step[1]
        let step = 0
        if (typeof propsStep === 'string') {
          if (propsStep.includes('%')) {
            step = (trackWidth - sliderWidth) * Number(propsStep.replace('%', '')) / 100
          } else {
            step = Number(propsStep) * (trackWidth - sliderWidth) / (props.max - props.min)
          }
        } else if (typeof propsStep === 'number') {
          step = propsStep * (trackWidth - sliderWidth) / (props.max - props.min)
        } else {
          return console.error('[EPR SLIDER BUTTON]: 步进的值只能为：整数，数字型字符串 或 带百分号的字符串，如：[1, "2", "5%"]')
        }
        if (offsetX > sPos) {
          if (sPos + step < trackWidth - sliderWidth) {
            slider.value.style.left = (sPos + step) + 'px'
          } else {
            slider.value.style.left = (trackWidth - sliderWidth) + 'px'
          }
        } else {
          if (sPos - step > 0) {
            slider.value.style.left = (sPos - step) + 'px'
          } else {
            slider.value.style.left = '0px'
          }
        }
        returnValue()
      }
    }
    // 长按事件
    function longpress (e: MouseEvent) {
      timer1 = 0
      const offsetX = e.offsetX
      const propsStep = props.step[2]
      let step = 0
      if (typeof propsStep === 'string') {
        if (propsStep.includes('%')) {
          step = (trackWidth - sliderWidth) * Number(propsStep.replace('%', '')) / 100
        } else {
          step = Number(propsStep) * (trackWidth - sliderWidth) / (props.max - props.min)
        }
      } else if (typeof propsStep === 'number') {
        step = propsStep * (trackWidth - sliderWidth) / (props.max - props.min)
      } else {
        return console.error('[EPR SLIDER BUTTON]: 步进的值只能为：整数，数字型字符串 或 带百分号的字符串，如：[1, "2", "5%"]')
      }
      timer2 =setInterval(() => {
        const sPos = getSliderLeft()
        if (offsetX > sPos) {
          if (sPos + step < trackWidth - sliderWidth) {
            slider.value.style.left = (sPos + step) + 'px'
          } else {
            slider.value.style.left = (trackWidth - sliderWidth) + 'px'
          }
        } else {
          if (sPos - step > 0) {
            slider.value.style.left = (sPos - step) + 'px'
          } else {
            slider.value.style.left = '0px'
          }
        }
        returnValue()
      }, 250)
    }
    // 清除定时器
    function clearTimer () {
      clearTimeout(timer1)
      clearInterval(timer2)
    }
    
    // 拖拽：鼠标按下
    function sliderMouseDown (evt: MouseEvent) {
      canMove = true
      const left = getSliderLeft()
      x = evt.clientX - left
    }
    // 拖拽：鼠标松开
    function sliderMouseUp () {
      canMove = false
    }
    // 拖拽：鼠标移出
    function sliderMouseOut () {
      canMove = false
    }
    // 拖拽：鼠标拖拽
    function sliderMouseMove (evt: MouseEvent) {
      if (canMove) {
        if (evt.clientX - x < 0) {
          slider.value.style.left = '0px'
        } else if (evt.clientX - x > trackWidth - sliderWidth) {
          slider.value.style.left = trackWidth - sliderWidth + 'px'
        } else {
          slider.value.style.left = evt.clientX - x + 'px'
        }
        returnValue()
      }
    }

    onMounted(() => {
      initAndCheck()
    })

    return {
      track,
      slider,
      handleChange,
      mousedownEvent,
      mouseupEvent,
      clearTimer,
      sliderMouseDown,
      sliderMouseMove,
      sliderMouseUp,
      sliderMouseOut
    }
  }
})
</script>
<style lang="scss">
.epr-btn{
  height: 28px;
  width: 256px;
  border: 1px solid #c6c6c6;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  .epr-btn-reduce{
    width: 28px;
    height: 28px;
    display: flex;
    cursor: pointer;
    align-items: center;
    box-sizing: border-box;
    justify-content: center;
    background-color: #f7f6f6;
    border-radius: 14px 0 0 14px;
    border-right: 1px solid #c6c6c6;
    .math-minus {
      box-sizing: border-box;
      position: relative;
      display: block;
      transform: scale(0.8);
      width: 16px;
      height: 2px;
      background: currentColor;
      border-radius: 10px;
      color: #9a9a9a;
    }
  }
  .epr-btn-track{
    flex: 1;
    height: 28px;
    display: flex;
    align-items: center;
    cursor: pointer;
    position: relative;
    .epr-btn-slider{
      position: absolute;
      left: 0px;
      display: inline-block;
      width: 32px;
      height: 18px;
      background-color: #2e99ce;
      border-radius: 30%;
    }
  }
  .epr-btn-increase{
    box-sizing: border-box;
    width: 28px;
    height: 28px;
    display: flex;
    cursor: pointer;
    align-items: center;
    justify-content: center;
    background-color: #f7f6f6;
    border-radius: 0 14px 14px 0;
    border-left: 1px solid #c6c6c6;
    .math-plus,
    .math-plus::after {
        display: block;
        box-sizing: border-box;
        background: currentColor;
        border-radius: 10px
    }
    .math-plus {
        margin-top: -2px;
        position: relative;
        transform: scale(0.8);
        width: 16px;
        height: 2px;
        color: #9a9a9a;
    }
    .math-plus::after {
        content: "";
        position: absolute;
        width: 2px;
        height: 16px;
        top: -7px;
        left: 7px
    }
  }
}
</style>