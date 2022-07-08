# 自适应时间轴播放器

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
| 事件名         | 说明                       | 参数   |
| -------------- | -------------------------- | ------ |
| animate-before | 当点击时间轴触发           | Object |
| animate-after  | 当点击时间轴动画完成后触发 | Object |
