<h1 align="center">EPR Components</h1>

<p align="center">
<a href="https://github.com/Hunlongyu/epr-components/issues" target="_blank">反馈</a>
</p>
<p align="center">
<img src="https://forthebadge.com/images/badges/built-with-love.svg">
<p>
<p align="center">
<img src="https://github.com/aleen42/badges/raw/master/src/visual_studio_code_flat_square.svg?sanitize=true">
<img src="https://github.com/aleen42/badges/raw/master/src/typescript_flat_square.svg?sanitize=true">
<img src="https://github.com/aleen42/badges/raw/master/src/vue.svg?sanitize=true">
</p>

### 🚀 体验地址 [Demo](https://hunlongyu.github.io/epr-components/)



### 🚄 使用教程

- 安装
  ```bash
  npm i epr-components
  // or
  yarn add epr-components
  ```
- 使用
  ```html
  <epr-slider-button :min='min' :max="max" :step="step" v-model="value" :precision="precision" @change="sliderBtnChange" />
  <epr-slider-input :min='min' :max="max" :step="step2" v-model="value2" :precision="precision" @change="sliderInputChange" />
  ```
  ```js
  import { eprSliderButton, eprSliderInput } from 'epr-components'
  import 'epr-components/dist/style.css'
  
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
        step: [1, '2', '3%'],
        step2: [1, 2],
        value: 50,
        value2: 30,
        precision: 3
      })
  
      function sliderBtnChange (e: number) {
        config.value = e
      }
  
      function sliderInputChange (e: number) {
        config.value2 = e
      }
  
      return {
        ...toRefs(config),
        sliderBtnChange,
        sliderInputChange
      }
    }
  })
  ```
### 📖 文档

- 属性
  | eprSliderButton | 说明                                                         | eprSliderInput | 说明                                                         |
  | :-------------: | ------------------------------------------------------------ | :------------: | ------------------------------------------------------------ |
  |      `min`      | `number` - 最小值                                            |     `min`      | `number` - 最小值                                            |
  |      `max`      | `number` - 最大值                                            |     `max`      | `number` - 最大值                                            |
  |     `step`      | `(string I number)[]` - 步进。第一位是鼠标点击左右两侧的加减按钮步进，第二位是鼠标点击滑块左右两侧滑道的步进值，第三位是鼠标长按滑块左右两侧滑道的步进值。步进值可以为`[整数, '整数'， '百分比']`： `[1, '2', '3%']`。 |     `step`     | `number[]` - 步进。第一位是鼠标点击左右两侧的加减按钮的进步，第二位是键盘上下按键的步进值。这里的步进值只能为： `[整数, 整数]`：`[1, 2]`。 但是第二位可以为空，既取消键盘的步进。 |
  |    `v-model`    | `number` - 当前值                                            |   `v-model`    | `number` - 当前值                                            |
  |   `precision`   | `number` - 精度                                              |  `precision`   | `number` - 精度                                              |



* 事件

  | eprSliderButton 、 eprSliderInput | 说 明                                                         |
  | --------------------------------- | ------------------------------------------------------------ |
  | `change(n: number, err?: string)` | 当用户点击鼠标或者键盘时触发，返回的 `n` 为改变后的值，`err` 为报错信息。|
  



### 💖 声明

MIT

Author: Hunlongyu
