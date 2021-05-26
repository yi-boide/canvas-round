
# 圆
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

# 半圆
## 使用示例
```
<template>
  <view class="content">
		<boideArch :val="1600" :twoVal="1500" :width="width" />
  </view>
</template>
<script>
import boideArch from '@/components/boide-arch/boide-arch.vue';
  export default {
    components: {
      boideArch
    },
    data() {
      return {
        width: uni.getSystemInfoSync().windowWidth
      }
    },
    methods: {
      changeCanvas(val) {
        console.log(val)
      }
    }
  }
</script>
```

## 使用说明

|  名称   | 类型  |  默认值   |  描述  |
|  ----  | ----  |  ----  | ----  |
| val  | number |  0  |  传入的初始值  |
| width  | number |  220  | 半圆的宽度  |
| border  | number |  20  | 圆弧弧线的大小  |
| lock  | boolean |  false  | 是否可以拖动  |
| step  | number |  50  | 移动次增加多少数值  |
| max  | number |  3000  | 最大值  |
| twoVal  | number |  0  | 第二层内圆数值  |
| pageBg  | String |  #FFF  | 背景色  |
| isTwoVal  | boolean |  true  | 是否展示第二个参数值  |
| twoVal  | number |  0  | 第二个数值  |
| filletColor  | String |  #FFA900  | 第二层内圆的颜色  |
| colorSatrt  | String |  #B8DFD0  | 圆弧的背景色  |
| colorEnd  | String |  #00a968  | 圆弧选中的颜色  |
| colorButton  | String |  #fff  | 控制点按钮的颜色  |

## 使用说明

|  名称   |  描述  |
|  ----  | ----  |
| change  |  回调事件，滑动结束后触发，返回滑动后的值  |

##  插槽说明

|  名称   | 描述  |
|  ----  | ----  |  ----  | ----  |
| default  | 自定义内容，样式请自行调整  |

示例代码
```
  <boideArch :val="val">
    <text>示例代码</text>
  </boideArch>
```