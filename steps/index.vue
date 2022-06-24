<template>
  <div class="container" ref="container" :key="key">
    <i
      class="el-icon-arrow-left"
      @click.stop="leftBtn"
      v-show="contentWidth - 1 > wrapWidth"
    ></i>
    <div class="barWrap" id="barWrap">
      <div class="wrap" :style="{ transform: `translateX(${wrapOffset}px)` }">
        <div class="blank"></div>
        <div class="content" :style="{ width: contentWidth + 'px' }">
          <div
            class="progress"
            :style="{
              background: themeColor || '#7074C8',
              width: persent ? persent + '%' : '0%',
            }"
          ></div>
          <div
            class="cntItem"
            :style="{
              left: `0%`,
            }"
          >
            <svg-icon
              :icon-class="'circle'"
              class="cntItemIcon"
              :style="{
                color: themeColor ? themeColor : '#7074C8',
              }"
            ></svg-icon>
          </div>
          <div
            class="cntItem"
            v-for="(item, key) in stepsData"
            :style="{
              left: `${item.realPersent}%`,
            }"
            :key="key"
          >
            <svg-icon
              :icon-class="'star_hollow'"
              class="cntItemIcon"
              :style="{
                color:
                  item.realPersent > persent
                    ? '#E6E6E6'
                    : themeColor || '#7074C8',
              }"
            ></svg-icon>
            <div class="bottomTag" :style="{ left: `-${item.tagOffset}px` }">
              {{ item.name }}
            </div>
          </div>
        </div>
        <div class="blank"></div>
      </div>
    </div>
    <i
      class="el-icon-arrow-right"
      @click.stop="rightBtn"
      v-show="contentWidth - 1 > wrapWidth"
    ></i>
  </div>
</template>

<script>
export default {
  name: "steps-bar",
  data() {
    return {
      key: 0,
      wrapOffset: 0,
      contentWidth: 0,
      wrapWidth: 450,
      showMoveBtn: false,
      baseWidth: 5,
    };
  },
  props: {
    themeColor: {
      type: String,
      default: "#7074C8",
    },
    persent: {
      type: Number,
      default: 0,
    },
    stepsData: {
      type: Array,
      default: [],
    },
  },
  watch: {
    stepsData: "setWrapWidth",
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.resizeFn);
  },
  mounted() {
    this.wrapWidth = this.$refs["container"].offsetWidth - 120;
    this.setWrapWidth();
    window.addEventListener("resize", this.resizeFn);
  },
  methods: {
    resizeFn() {
      this.wrapWidth = this.$refs["container"].offsetWidth - 120;
      this.setWrapWidth();
      console.log("onresize", this.wrapWidth);
    },
    formatClassStage(idx) {
      const stageName = [
        "一",
        "二",
        "三",
        "四",
        "五",
        "六",
        "七",
        "八",
        "九",
        "十",
        "十一",
        "十二",
        "十三",
        "十四",
        "十五",
        "十六",
        "十七",
        "十八",
        "十九",
        "二十",
        "二十一",
        "二十二",
        "二十三",
        "二十四",
      ];
      return `阶段${stageName[idx]}`;
    },
    // 固定stepsData中占比最少的阶段为100px，反推总宽度，最小宽度设置450
    setWrapWidth() {
      const list = [...this.stepsData];
      list.sort((pre, aft) => {
        return pre.position - aft.position;
      });
      //   baseWidth设置为5，最小显示宽度为500
      if (500 < this.wrapWidth) {
        this.baseWidth = this.wrapWidth / 100;
      } else {
        this.baseWidth = 5;
      }
      let width = 0;
      let prePersent = 0;
      this.stepsData.map((item, index) => {
        const itemWidth = item.position * this.baseWidth;
        item.tagOffset = itemWidth / 2 - 5;
        width += itemWidth;
        item.realPersent = item.position + prePersent;
        prePersent += item.position;
        item.name = this.formatClassStage(index);
      });
      console.log(
        "setWrapWidth",
        this.contentWidth,
        this.wrapWidth,
        this.baseWidth
      );
      this.contentWidth = width;
      if (this.contentWidth > this.wrapWidth) {
        this.showMoveBtn = true;
        if (this.baseWidth === 5) {
          this.wrapOffset = 50;
        }else{
          this.wrapOffset = 0;
        }
      }else{
          this.wrapOffset = 0;
        }
    },
    leftBtn() {
      let limit = 0;
      if (this.baseWidth === 5) {
        limit = 50;
      }
      console.log("leftBtn", this.wrapOffset);
      if (this.wrapOffset + 100 >= limit) {
        this.wrapOffset = limit;
      } else {
        this.wrapOffset += 100;
      }
    },
    rightBtn() {
      let limit = 30;
      if (this.baseWidth === 5) {
        limit = 0;
      }
      console.log(
        "rightBtn",
        this.wrapOffset,
        this.wrapWidth,
        this.contentWidth
      );
      if (this.wrapOffset <= this.wrapWidth - this.contentWidth  + 100) {
        this.wrapOffset = this.wrapWidth - this.contentWidth  ;
      } else {
        this.wrapOffset -= 100;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  .barWrap {
    overflow: hidden;
    height: 60px;
    // width: 450px;
    justify-content: center;
    white-space: nowrap;
    cursor: pointer;
    display: flex;
    .wrap {
      transition: all 0.5s;
      transform: translateX(0);
      display: flex;
      box-sizing: content-box;
      align-items: center;
    }
    .blank {
      width: 30px;
    }
    .content {
      height: 3px;
      border-radius: 1.5px;
      position: relative;
      background: #f3f3f3;
      .progress {
        height: 3px;
      }
      .cntItem {
        width: 10px;
        height: 10px;
        border-radius: 6px;
        background: #fff;
        position: absolute;
        left: 0;
        top: -4px;
        box-sizing: border-box;
        .cntItemIcon {
          position: relative;
          left: -2.5px;
          top: -2.5px;
          fill: currentColor;
        }
        .bottomTag {
          position: absolute;
          left: 50%;
          bottom: -25px;
          transform: translateX(-50%);
          font-size: 12px;
          color: #999;
          background: #eee;
          line-height: 16px;
          border-radius: 8px;
          padding: 0 8px;
        }
      }
    }
  }
  ::-webkit-scrollbar {
    width: 0 !important;
  }
  ::-webkit-scrollbar {
    width: 0 !important;
    height: 0;
  }
  .el-icon-arrow-right,
  .el-icon-arrow-left {
    flex-shrink: 0;
    font-size: 30px;
    cursor: pointer;
    &:active {
      background: #eee;
    }
  }
  ::v-deep {
    .svg-icon {
      vertical-align: initial;
    }
  }
}
</style>
