# 自适应时间轴播放器

## 前言
* 这是一个从0到1实现的时间轴播放器，除了时间格式化用了第三库dayjs以外，其它的都是使用纯源生的js实现的，兼容性IE8吧，这里小小的夸一下
* 非常轻量，包的大小在100kb以内，压缩前后都是
* 从刚开始的琢磨不透，到最后的云淡风轻，可算把这个时间轴播放器给完成了
## 效果
![image](/src/assets/gif/time-player.gif)
## 技术栈
<a href="https://github.com/vuejs/vue/tree/v2.6.14">
  <img src="https://img.shields.io/badge/vue-2.16.4-brightgreen" alt="vue">
</a>
<a href="https://dayjs.gitee.io/zh-CN/">
  <img src="https://img.shields.io/badge/dayjs-1.11.3-brightgreen" alt="vue">
</a>

## 文件目录
```shell
│  .babelrc
│  .gitignore
│  index.html
│  package.json
│  README.md
│  rollup.config.build.js
│  rollup.config.dev.js  
│  yarn.lock
│
├─packages
│  └─DTimePlayer
│      │  index.js       
│      │
│      └─src
│              indes.scss
│              index.vue
│
└─src
        index.js
```

## 如何安装
* 如果你使用npm
```shell
npm i @dienrt/time-player --save
```
* 或者使用yarn
```shell
yarn add @dinert/time-player
```

## 如何使用
```js
  import DTimePlayer from '@dinert/time-player'
  <d-time-player></d-time-player>
  export default {
    components: {
      DTimePlayer
    }
  }
```

## 属性
| 参数          | 说明              | 类型   | 可选值 | 默认值              |
| ------------- | ----------------- | ------ | ------ | ------------------- |
| startTime     | 开始时间          | Date   | 一     | 当前时间的前两天    |
| endTime       | 结束时间          | Date   | 一     | 当前时间的后两天    |
| currentTime   | 当前时间          | Date   | 一     | new Date()          |
| stopTime      | 时间轴停止的时间  | Date   | 一     | new Date()          |
| formatFooter  | 底部时间格式化    | String | 一     | YYYY年MM月DD日      |
| formatTooltip | tooltip时间格式化 | String | 一     | YYYY年MM月DD日 HH时 |
| interval      | 24小时时间的间隔  | Number | 一     | 3                   |
| delay         | 播放时间的间隔    | Number | 一     | 2000                |
## 方法
| name      | 说明     |
| --------- | -------- |
| startPlay | 开始播放，请求数据完成，开始播放 |
| stopPlay  | 停止播放，后台请求数据的时间就可以停止播放 |


## 事件
| 事件名         | 说明                       | 参数   |
| -------------- | -------------------------- | ------ |
| animate-before | 当点击时间轴触发           | Object |
| animate-after  | 当点击时间轴动画完成后触发 | Object |

## 创作不易😊，如果觉得好用的话，可以请作者喝杯咖啡😘
<img src="https://user-images.githubusercontent.com/35824497/217978995-c5abeb58-4bb7-40bf-abbf-3f0c3b6d1b97.jpg" alt="支付宝" title="支付宝">
<img src="https://user-images.githubusercontent.com/35824497/217979009-d6eacb3f-6e5e-43b5-87fa-2689f69d6785.jpg" alt="微信" title="微信">


