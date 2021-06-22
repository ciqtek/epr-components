<template>
  <div class="epr-btn">
    <span class="epr-btn-reduce" @mousedown="btnMouseDownEvent('left', $event)" @mouseup="btnMouseUpEvent('left', $event)" @mouseout="clearBtnTimer">
      <span class="gg-chevron-left"></span>
    </span>
    <span class="epr-btn-track" @mousedown.self="trackMouseDownEvent" @mouseup.self="trackMouseUpEvent" @mouseout="clearTrackTimer" ref="track">
      <span class="epr-btn-slider" ref="slider" @mousedown="sliderMouseDown" @mouseup="sliderMouseUp" @mousemove="sliderMouseMove" @mouseout="sliderMouseOut"></span>
    </span>
    <span class="epr-btn-increase" @mousedown="btnMouseDownEvent('right', $event)" @mouseup="btnMouseUpEvent('right', $event)">
      <span class="gg-chevron-right"></span>
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
      default: [1, 2, '5%', '10%']
    },
    precision: {
      type: Number,
      default: 2
    }
  },
  emits: ['change'],
  setup (props, { emit }) {
    const slider = ref() // 【滑块】 dom
    const track = ref() // 【滑道】 dom
    let sliderWidth = 0 // 【滑块】的宽度
    let trackWidth = 0 // 【滑道】的宽度
    let btnTimer1: any = 0 // 判断【按钮】是点击还是长按
    let btnTimer2: any = 0 // 【按钮】长按时，间隔时间
    let trackTimer1: any = 0 // 判断【滑道】是点击还是长按
    let trackTimer2: any = 0 // 【滑道】长按时，间隔时间
    let canMove = false // 判断【滑块】能否拖拽
    let x = 0 // 【滑块】拖拽的位移距离

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

    // 【按钮】：按下
    function btnMouseDownEvent (pos: string, e: MouseEvent) {
      btnTimer1 = setTimeout(() => {
        btnLongpress(pos, e)
      }, 300)
    }
    // 【按钮】：长按
    function btnLongpress (pos: string, e: MouseEvent) {
      btnTimer1 = 0
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
      btnTimer2 =setInterval(() => {
        const sPos = getSliderLeft()
        if (pos === 'left') {
          if (sPos - step >= 0) {
            slider.value.style.left = (sPos - step) + 'px'
          } else {
            slider.value.style.left = '0px'
          }
        }
        if (pos === 'right') {
          if (sPos + step <= trackWidth - sliderWidth) {
            slider.value.style.left = (sPos + step) + 'px'
          } else {
            slider.value.style.left = (trackWidth - sliderWidth) + 'px'
          }
        }
        returnValue()
      }, 250)
    }
    // 【按钮】：单击
    function btnMouseUpEvent (pos: string, e: MouseEvent) {
      clearTimeout(btnTimer1)
      clearInterval(btnTimer2)
      if (btnTimer1 !== 0) {
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
        const sPos = getSliderLeft()
        if (pos === 'left') {
          if (sPos - step >= 0) {
            slider.value.style.left = (sPos - step) + 'px'
          } else {
            slider.value.style.left = '0px'
          }
        }
        if (pos === 'right') {
          if (sPos + step <= trackWidth - sliderWidth) {
            slider.value.style.left = (sPos + step) + 'px'
          } else {
            slider.value.style.left = (trackWidth - sliderWidth) + 'px'
          }
        }
        returnValue()
      }
    }
    // 【按钮】：清理定时器
    function clearBtnTimer () {
      clearTimeout(btnTimer1)
      clearInterval(btnTimer2)
    }

    // 【滑道】：按下
    function trackMouseDownEvent (e: MouseEvent) {
      trackTimer1 = setTimeout(() => {
        trackLongpress(e)
      }, 300)
    }
    // 【滑道】：单击
    function trackMouseUpEvent (e: MouseEvent) {
      clearTimeout(trackTimer1)
      clearInterval(trackTimer2)
      if (trackTimer1 !== 0) {
        const offsetX = e.offsetX
        const sPos = getSliderLeft()
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
    // 【滑道】：长按
    function trackLongpress (e: MouseEvent) {
      trackTimer1 = 0
      const offsetX = e.offsetX
      const propsStep = props.step[3]
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
      trackTimer2 =setInterval(() => {
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
    // 【滑道】：清除定时器
    function clearTrackTimer () {
      clearTimeout(trackTimer1)
      clearInterval(trackTimer2)
    }

    // 【拖拽】：鼠标按下
    function sliderMouseDown (evt: MouseEvent) {
      canMove = true
      const left = getSliderLeft()
      x = evt.clientX - left
    }
    // 【拖拽】：鼠标松开
    function sliderMouseUp () {
      canMove = false
    }
    // 【拖拽】：鼠标移出
    function sliderMouseOut () {
      canMove = false
    }
    // 【拖拽】：鼠标拖拽
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
      btnMouseDownEvent,
      btnMouseUpEvent,
      clearBtnTimer,
      trackMouseDownEvent,
      trackMouseUpEvent,
      clearTrackTimer,
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
  height: 100%;
  width: 100%;
  border: 1px solid #c6c6c6;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  .epr-btn-reduce{
    width: 28px;
    height: 100%;
    display: flex;
    cursor: pointer;
    align-items: center;
    box-sizing: border-box;
    justify-content: center;
    background-color: #f7f6f6;
    border-radius: 14px 0 0 14px;
    border-right: 1px solid #c6c6c6;
    .gg-chevron-left {
      color: #a0a0a0;
      box-sizing: border-box;
      position: relative;
      display: block;
      transform: scale(0.8);
      width: 22px;
      height: 22px;
      border: 2px solid transparent;
      border-radius: 100px
    }
    .gg-chevron-left::after {
      content: "";
      display: block;
      box-sizing: border-box;
      position: absolute;
      width: 10px;
      height: 10px;
      border-bottom: 2px solid;
      border-left: 2px solid;
      transform: rotate(45deg);
      left: 6px;
      top: 4px
    }
  }
  .epr-btn-track{
    flex: 1;
    height: 100%;
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
      background-image: linear-gradient(#009efa, #2256a7);
      border-radius: 9px;
      &:hover{
        cursor: w-resize;
      }
    }
  }
  .epr-btn-increase{
    box-sizing: border-box;
    width: 28px;
    height: 100%;
    display: flex;
    cursor: pointer;
    align-items: center;
    justify-content: center;
    background-color: #f7f6f6;
    border-radius: 0 14px 14px 0;
    border-left: 1px solid #c6c6c6;
    .gg-chevron-right {
      color: #a0a0a0;
      box-sizing: border-box;
      position: relative;
      display: block;
      transform: scale(0.8);
      width: 22px;
      height: 22px;
      border: 2px solid transparent;
      border-radius: 100px;
    }
    .gg-chevron-right::after {
      content: "";
      display: block;
      box-sizing: border-box;
      position: absolute;
      width: 10px;
      height: 10px;
      border-bottom: 2px solid;
      border-right: 2px solid;
      transform: rotate(-45deg);
      right: 6px;
      top: 4px
    }
  }
}
</style>