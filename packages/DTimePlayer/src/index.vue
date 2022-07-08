<template>
  <div class="dTimePlayer">
    <div class="dTimePlayer-left">
      <div class="dTimePlayer-left-t"></div>
      <div class="dTimePlayer-left-b">
        <span
          :class="['dTimePlayer-left-b-start', isPlay ? 'stop' : '']"
          @click="timePlay"
        ></span>
      </div>
    </div>
    <div class="dTimePlayer-center">
      <div class="dTimePlayer-center-t">
        <div class="dTimePlayer-center-t-bar" ref="bar"></div>
        <div class="dTimePlayer-center-t-tooltip" ref="tooltip">
          {{ formatTooltipText }}
        </div>
        <div
          class="dTimePlayer-center-t-tooltipTemp"
          ref="tempTooltip"
          v-show="isTempTooltip"
        >
          {{ formatTempTooltipText }}
        </div>
        <ul
          class="dTimePlayer-center-t-ul"
          @click="timeClick($event)"
          @mousemove="timeMouseEnter($event)"
          @mouseleave="timeMouseLeave($event)"
        >
          <li
            v-for="item in items"
            :key="item.time"
            :style="{ width: item.width }"
          ></li>
        </ul>
      </div>
      <div class="dTimePlayer-center-c">
        <ul class="dTimePlayer-center-c-ul">
          <li
            class="dTimePlayer-center-c-ul-li"
            v-for="item in items"
            :key="item.time"
            :style="{ width: item.width }"
          >
            <span
              v-for="hour in item.hours"
              :key="hour.id"
              :style="{ left: hour.left }"
              >{{ hour.text }}</span
            >
          </li>
        </ul>
      </div>
      <div class="dTimePlayer-center-b">
        <ul class="dTimePlayer-center-b-ul">
          <li
            class="dTimePlayer-center-b-ul-li"
            v-for="item in items"
            :key="item.time"
            :style="{ width: item.width }"
          >
            {{ item.time }}
          </li>
        </ul>
      </div>
    </div>
    <div class="dTimePlayer-right">
      <span class="dTimePlayer-right-now" @click="timeNow">回到当前</span>
    </div>
  </div>
</template>  

<script>
import dayjs from "dayjs";
export default {
  name: "DTimePlayer",
  props: {
    startTime: {
      // 开始时间
      type: Date,
      default: () => {
        return new Date(dayjs().subtract(2, "day").startOf("day"));
      },
    },
    endTime: {
      // 结束时间
      type: Date,
      default: () => {
        return new Date(dayjs().add(2, "day").startOf("day"));
      },
    },
    currentTime: {
      // 时间轴当前时间
      type: Date,
      default: () => {
        return new Date(dayjs().subtract(1, "day").startOf("hour").toDate());
      },
    },
    // 时间轴停止的时间
    stopTime: {
      type: Date,
      default: () => {
        return new Date(dayjs().add(2, "day").startOf("day"));
      },
    },
    // 底部时间格式化
    formatFooter: {
      type: String,
      default: "YYYY年MM月DD日",
    },
    // tooltip时间格式化
    formatTooltip: {
      type: String,
      default: "YYYY年MM月DD日 HH时",
    },
    // 24小时时间的间隔
    interval: {
      type: Number,
      default: 3,
    },
    delay: {
      // 播放时间间隔
      type: Number,
      default: 2000,
    },
  },
  data() {
    return {
      isAnimate: false, // 动画是否完成
      isTempTooltip: false, // 是否显示暂时的tooltip
      currentTempTime: null || this.currentTime, // 当前
      hoverTime: null || this.currentTime, // 时间移动过去的tooltip
      isPlay: false,
      timeTimeout: null,
      config: {},
    };
  },
  computed: {
    formatTooltipText() {
      return dayjs(this.currentTempTime).format(this.formatTooltip);
    },
    formatTempTooltipText() {
      return dayjs(this.hoverTime).format(this.formatTooltip);
    },
    items() {
      let result = [];
      let hours = 3600 * 1000;
      let daysTimestamp = hours * 24;
      let time = dayjs(this.startTime).valueOf();
      let items = dayjs(this.endTime).diff(dayjs(this.startTime), "days");
      let width = 100 / items + "%";
      let left = 100 / (24 / this.interval);
      for (let i = 0; i < items; i++) {
        let tempArr = [];
        let count = 0;
        for (let j = 1; j < 24; j++) {
          if (j % this.interval === 0) {
            count++;
            tempArr.push({
              text: j,
              left: left * count + "%",
              id: Math.random(),
            });
          }
        }
        result.push({
          hours: tempArr,
          time: dayjs(time).format(this.formatFooter),
          width,
        });
        time += daysTimestamp;
      }
      return result;
    },
  },
  mounted() {
    this.bar = this.$refs.bar;
    this.tooltip = this.$refs.tooltip;
    this.tempTooltip = this.$refs.tempTooltip;
    this.animateAfter();
    this.autoMove(this.getWidth());
  },
  methods: {
    autoMove(width, event) {
      // 自动播放
      !event && this.isPlay && this.addTime(this.currentTempTime);
      width = width || this.getWidth();

      // 判断超出距离设置为100
      if(width > 100) {
        width = 100
      }

      this.bar.style.width = width + "%";
      this.tooltip.style.left = width + "%";

      // 判断结束时间是否大于当前时间，如果大于就停止播放
      if(dayjs(this.stopTime).startOf('hours').valueOf() <= dayjs(this.currentTempTime).valueOf()) {
        this.isPlay = false
      }
      if (this.isPlay) {
        this.timeTimeout && clearTimeout(this.timeTimeout);
        this.timeTimeout = setTimeout(this.autoMove, this.delay);
      }else {
        this.timeTimeout && clearTimeout(this.timeTimeout);
      }
    },
    getWidth() {
      // 时间转换为宽度计算
      let startEndDiffer =
        dayjs(this.endTime).valueOf() - dayjs(this.startTime).valueOf();
      let currentEndDiffer =
        dayjs(this.endTime).valueOf() - dayjs(this.currentTempTime).valueOf();
      currentEndDiffer = startEndDiffer - currentEndDiffer;
      let percentDiffer = (currentEndDiffer / startEndDiffer) * 100;
      return percentDiffer;
    },

    // 宽度转换为时间计算
    getConfig(event) {
      let el = event.currentTarget;
      let x = event.layerX + 1;
      let width = el.offsetWidth;
      let percent = x / width;
      let startEndDiffer =
        dayjs(this.endTime).valueOf() - dayjs(this.startTime).valueOf();
      let time =
        dayjs(this.startTime).valueOf() +
        dayjs(startEndDiffer * percent).valueOf();
      time = dayjs(time).format("YYYY-MM-DD HH:mm:ss");
      percent = percent * 100;
      return {
        time,
        percent,
        width: x,
      };
    },

    // 时间轴点击
    timeClick(event) {
      this.config = this.getConfig(event);

      // 判断结束时间是否大于当前时间，如果大于就停止播放
      if(dayjs(this.stopTime).startOf('hours').valueOf() <= dayjs(this.config.time).valueOf()) {
        return 
      }

      this.changeTime(this.config.time);

      this.autoMove(this.config.percent, event);
      this.$emit("animate-before", this.config);
    },

    // 动画执行完的回调
    animateAfter() {
      this.bar.addEventListener("transitionend", () => {
        console.log(this.config, "aaa");
        this.$emit("animate-after", this.config);
      });
    },

    // 改变时间
    changeTime(time) {
      this.currentTempTime = dayjs(time).valueOf();
    },
    // 增加时间
    addTime(time, type = "hour") {
      this.currentTempTime = dayjs(time).add(1, type);
    },

    // 鼠标移入
    timeMouseEnter(event) {
      var config = this.getConfig(event);
      this.tempTooltip.style.left = config.percent + "%";
      this.hoverTime = config.time;
      this.isTempTooltip = true;
    },
    // 鼠标移出
    timeMouseLeave() {
      this.isTempTooltip = false;
    },
    // 播放
    timePlay() {

      // 判断结束时间是否大于当前时间，如果大于就停止播放
      if(dayjs(this.stopTime).startOf('hours').valueOf() <= dayjs(this.currentTempTime).valueOf()) {
        return 
      }

      this.isPlay = !this.isPlay;
      if (this.isPlay) {
        this.timeTimeout && clearTimeout(this.timeTimeout);
        this.timeTimeout = setTimeout(this.autoMove, this.delay);
      } else {
        clearTimeout(this.timeTimeout);
      }
    },

    // 回到当前
    timeNow() {
      this.changeTime(this.currentTime);
      this.autoMove();
    },
  },
};
</script>

<style lang="scss" scoped>
@import "./indes.scss";
</style>