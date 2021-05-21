
## 使用示例
```
<template>
  <view class="content">
    <canvasRound
      :val="val"
      :id="charts.id"
      :width="charts.width"
      :border="charts.border"
      @change="changeuCharts"
    ></canvasRound>
  </view>
</template>
<script>
import canvasRound from '@/components/canvas-round/index.vue';
  export default {
    components: {
      canvasRound
    },
    data() {
      return {
        val: 0,
        charts: {
          id: 'canvas-round',
          width: uni.getSystemInfoSync().windowWidth - 40,
          border: 16,
          pageBg: '#fff'
        }
      }
    },
    methods: {
      changeuCharts(val) {
        this.val = val
      }
    }
  }
</script>
```

## 使用说明

|  名称   | 类型  |  默认值   |  描述  |
|  ----  | ----  |  ----  | ----  |
| val  | number |  0  |  传入的初始值  |
| width  | number |  220  | 圆的直径  |
| border  | number |  35  | 圆弧的大小  |
| pageBg  | String |  #FFF  | 背景色  |
| twoVal  | number |  0  | 第二个参数值  |
| isTwoVal  | boolean |  true  | 是否展示第二个参数值  |
| colorSatrt  | String |  #FFD2E0  | 渐变色开始值，圆弧的初始值  |
| colorEnd  | String |  #ff0163  | 渐变色最终值  |
| colorButton  | String |  #fff  | 按钮外圈的颜色  |
| circle  | number |  300  | 一圈的数值  |

## 使用说明

|  名称   |  描述  |
|  ----  | ----  |
| change  |  回调事件，滑动结束后触发，返回滑动后的值  |

##  插槽说明

|  名称   | 描述  |
|  ----  | ----  |  ----  | ----  |
| default  | 圆环中心的内容  |

示例代码
```
  <canvasRound :val="val">
    <text>示例代码</text>
  </canvasRound>
```