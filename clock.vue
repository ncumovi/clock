<template>
  <div class="clock" :style="{ width: width + 'px', height: width + 'px' }">
    <!-- 刻度 -->
    <div id="scale" v-if="hasScale"></div>

    <div class="outer-clock-face">
      <!-- 数字 -->
      <div
        id="clock-num-wrap"
        :class="{ 'only-show-mainnum': onlyShowMainNum }"
      ></div>

      <!-- 时分秒针 -->
      <div class="inner-clock-face">
        <div
          class="hand hour-hand"
          :style="{ transform: 'rotate(' + hourDegrees + 'deg)' }"
        ></div>
        <div
          class="hand min-hand"
          :style="{ transform: 'rotate(' + minsDegrees + 'deg)' }"
        ></div>
        <div
          class="hand second-hand"
          :style="{ transform: 'rotate(' + secondsDegrees + 'deg)' }"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    width: {
      type: Number,
      default: 300,
    },
    hasScale: {
      type: Boolean,
      default: true,
    },
    onlyShowMainNum: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      hourDegrees: "",
      minsDegrees: "",
      secondsDegrees: "",
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.initClock();
      this.setDate();

      setInterval(this.setDate, 1000);
    });
  },
  methods: {
    initClock() {
      const clockDom = document.querySelector("#clock-num-wrap");
      const scalesDom = document.querySelector("#scale");
      let numDomStr = "";
      let scalesDomStr = "";
      //添加数字
      for (let i = 1; i < 13; i++) {
        numDomStr += `<div class="clock-num ${
          i % 3 == 0 ? "en_num" : ""
        }" style='transform:rotate(${30 * i + 90}deg);padding-left:${
          this.hasScale ? "10%" : "2%"
        }' >
            <span class="num" style='transform:rotate(${
              270 - i * 30
            }deg)'>${i}</span>
            </div>`;
      }
      clockDom.innerHTML = numDomStr;

      if (this.hasScale) {
        //添加刻度
        for (let i = 0; i < 60; i++) {
          scalesDomStr += `<li class="" style='transform:rotate(${
            6 * i
          }deg); transform-origin: center ${this.width / 2 - 7}px;' >
            </li>`;
        }
        scalesDom.innerHTML = scalesDomStr;
      }
    },
    setDate() {
      const now = new Date();

      const seconds = now.getSeconds();
      this.secondsDegrees = (seconds / 60) * 360 + 90;

      const mins = now.getMinutes();
      this.minsDegrees = (mins / 60) * 360 + (seconds / 60) * 6 + 90;

      const hour = now.getHours();
      this.hourDegrees = (hour / 12) * 360 + (mins / 60) * 30 + 90;
    },
  },
};
</script>

<style lang="scss" scoped>
::v-deep.clock {
  background: #282828;
  border: 7px solid #282828;
  box-shadow: -4px -4px 10px rgba(67, 67, 67, 0.5),
    inset 4px 4px 10px rgba(0, 0, 0, 0.5),
    inset -4px -4px 10px rgba(67, 67, 67, 0.5), 4px 4px 10px rgba(0, 0, 0, 0.3);
  border-radius: 50%;
  margin: 0 auto;
  position: relative;

  .outer-clock-face {
    width: 100%;
    height: 100%;
    border-radius: 100%;
    overflow: hidden;
    #clock-num-wrap {
      &.only-show-mainnum {
        .clock-num {
          visibility: hidden;
        }
        .en_num {
          font-size: 12px;
          visibility: visible;
        }
      }
    }
    /* 钟表数字 */
    .clock-num {
      color: #fff;
      width: 100%;
      height: 40px;
      position: absolute;
      top: 50%;
      margin-top: -20px;
      left: 0;
      display: flex;
      align-items: center;
      box-sizing: border-box;
      font-size: 12px;
    }

    .en_num {
      font-size: 14px;
    }

    .inner-clock-face {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 50%;
      height: 50%;
      transform: translate(-50%, -50%);
      background: #282828;
      -webkit-border-radius: 100%;
      -moz-border-radius: 100%;
      border-radius: 100%;
      z-index: 1;
    }

    .inner-clock-face::before {
      content: "";
      position: absolute;
      top: 50%;
      left: 50%;
      width: 10%;
      height: 10%;
      border-radius: 50%;
      transform: translate(-50%, -50%);
      background: #4d4b63;
      z-index: 11;
    }

    /* 时分秒 */
    .hand {
      width: 50%;
      right: 50%;
      height: 6px;
      background: #61afff;
      position: absolute;
      top: 50%;
      margin-top: -3px;
      border-radius: 6px;
      transform-origin: 100%;
      transform: rotate(90deg);
      transition-timing-function: cubic-bezier(0.1, 2.7, 0.58, 1);
    }

    .hand.hour-hand {
      background: red;
      width: 30%;
      z-index: 3;
    }

    .hand.min-hand {
      height: 3px;
      z-index: 10;
      width: 40%;
      margin-top: -1.5px;
    }

    .hand.second-hand {
      background: #ee791a;
      width: 45%;
      height: 2px;
      margin-top: -1px;
    }
  }

  /* 刻度 */
  #scale {
    list-style: none;
    width: 100%;
    height: 100%;
    position: relative;
    li {
      background: #fff;
      position: absolute;
      height: 12px;
      width: 2px;
      left: 50%;
      margin-left: -1px;
      transform-origin: center 15vw;
      &:nth-child(5n + 1) {
        width: 4px;
        height: 15px;
      }
    }
  }
}
</style>
