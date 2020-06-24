##### 一、安装

> npm install svg-sprite-loader --save-dev

##### 二、创建组件SvgIcon

`````vue
<template>
  <svg :class="svgClass" aria-hidden="true" >
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
  export default {
    name: 'svg-icon',
    props: {
      iconClass: {
        type: String,
        required: false
      },
      className: {
        type: String
      }
    },
    computed: {
      iconName() {
        console.log(this.iconClass);
        return `#icon-${this.iconClass}`
      },
      svgClass() {
        if (this.className) {
          return 'svg-icon ' + this.className
        } else {
          return 'svg-icon'
        }
      }
    }
  }
</script>

<style scoped>
  .svg-icon {
    width: 1.2em;
    height: 1.2em;
    vertical-align: -0.18em;
    fill: currentColor;
    overflow: hidden;
  }
</style>
`````

##### 三、创建存放svg的文件夹

![image-20200526102904035](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200526102904035.png)

##### （1）svg存放.svg

##### （2）index.js代码如下

`````javascript
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon'// svg组件

// register globally
Vue.component('svg-icon', SvgIcon)

const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
`````

##### 四、在main.js导入

`````javascript
import '@/icons'
`````

##### 五、配置vue.config.js

````javascript
const path = require('path')
function resolve(dir) {
  return path.join(__dirname, '.', dir)
}
module.exports = {
  chainWebpack: config => {
    config.module.rules.delete("svg"); //重点:删除默认配置中处理svg,
    //const svgRule = config.module.rule('svg')
    //svgRule.uses.clear()
    config.module
      .rule('svg-sprite-loader')
      .test(/\.svg$/)
      .include
      .add(resolve('src/icons')) //处理svg目录
      .end()
      .use('svg-sprite-loader')
      .loader('svg-sprite-loader')
      .options({
        symbolId: 'icon-[name]'
      })
  }
}
````

##### 六、使用组件<svg-icon></svg-icon>

````vue
//qq是icon/svg下的.svg文件的name
<svg-icon icon-class="qq"></svg-icon>
````

