<template>
  <div class="home">
    <transition name="trans">
      <div class="sm-top-box" v-if="curTab == 1">DEMO</div>
    </transition>
    <div class="restrain">
      <div
        class="top_floatBox"
        ref="tpbox"
        :style="[
          { transform: `translate(0px,${topBoxtranslate}px)` },
          restrainTsStyle,
        ]"
        @touchstart.prevent="tptouchstart"
        @touchmove.prevent="tptouchmove"
        @touchend.prevent="tptouchend"
      >
        <div class="container">
          行政長官林鄭月娥表示，大部分從海外或內地入境本港的「豁免群組」免檢疫安排將會取消，只會剩下一些非常緊急和必要，與本港日常運作有關的人員，例如跨境貨車司機，才可以豁免檢疫往來兩地，希望令中央對香港的防疫措施更有信心。
          林鄭月娥出席行政會議前表示，特區政府非常重視香港居民可以免檢疫返回內地，但暫時沒有制定通關日期，目標是愈早愈好，而即使日後可以免檢疫通關都會是有序進行，初期會實施配額，不會回復疫情前開放好多口岸讓市民隨便返回內地。
          被問到有外商表示，因香港持續維持出入境防疫限制，可能影響國際金融中心地位。林鄭月娥說，如要與內地通關，香港的防疫措施就要與內地配合，讓內地有信心讓港人免檢疫入境，而外防輸入的工作非常重要，如香港放寬外地人士來港的防疫限制，或採取與病毒共存策略，將減低與內地恢復通關的機會。
        </div>
        <!--箭头0-->
        <transition name="myfade">
          <div class="toparrbox" v-if="curTab === 0">
            <div
              class="iconfont icon-tubiaozhizuo- tab0"
              :style="{
                transform: `rotate(${
                  topBoxtranslate < -this.miniY ? '180deg' : '0deg'
                })`,
              }"
            ></div>
          </div>
        </transition>
      </div>
      <div
        class="mainbox scroll"
        :style="[
          {
            transform: `translate(0px,${topBoxtranslate}px)`,
          },
          restrainTsStyle,
        ]"
        @touchstart="mbtouchstart"
        @touchmove="mbtouchmove"
        @touchend="mbtouchend"
      >
        <transition name="arrowbox">
          <div class="arrowbox" v-if="curTab === 1">
            <div
              class="iconfont icon-tubiaozhizuo- tab1"
              :style="{
                transform: `rotate(${
                  this.topBoxtranslate >
                  -this.$refs.tpbox.offsetHeight + this.miniY
                    ? '180deg'
                    : '0deg'
                })`,
              }"
            ></div>
          </div>
        </transition>
        <div class="container" @scroll="mbscroll" ref="slist">
          <div class="list">
            <div class="item" v-for="item in slist" :key="item.id">
              <img
                src="https://oss-xpc0.xpccdn.com/uploadfile/article/2020/06/23/5ef183409997f.jpeg@750w.jpg"
                alt=""
                style="width: 100%"
              />
              这是第{{ item.id }}条数据
            </div>
          </div>
          <div v-if="freshing" class="refreshing">....数据加载中.....</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  components: {},
  data() {
    return {
      istouching: false,
      tpTouch: {
        startposition: {
          clientY: null,
        },
      },
      mbTouch: {
        startposition: {
          clientY: null,
          scrollTop: null,
        },
      },
      topBoxtranslate: 0,
      miniY: 40,
      curTab: 0,
      slist: [],
      topboxHeight: 100,
      freshing: false,
    };
  },
  created() {
    for (var i = 0; i < 10; i++) {
      this.slist.push({ id: i });
    }
  },
  computed: {
    restrainTsStyle: {
      get() {
        if (!this.istouching) {
          return {
            transition: `transform 0.5s ease-out`,
          };
        } else {
          return { transition: "null" };
        }
      },
    },
  },
  methods: {
    tptouchstart(e) {
      this.istouching = true;
      this.tpTouch.startposition = {
        clientY: e.targetTouches[0].clientY,
      };
    },
    tptouchmove(e) {
      let ymove = parseInt(
        e.targetTouches[0].clientY - this.tpTouch.startposition.clientY
      );
      if (ymove > 0) {
        return;
      }
      if (ymove < -24) {
        let znMv = this.damping(ymove, 500);
        this.topBoxtranslate = -(Math.abs(znMv) + 21);
      } else {
        this.topBoxtranslate = ymove;
      }
    },
    tptouchend(e) {
      this.istouching = false;
      if (this.topBoxtranslate < -this.miniY) {
        this.topBoxtranslate = -this.$refs.tpbox.offsetHeight;
        this.curTab = 1;
      } else {
        this.topBoxtranslate = 0;
      }
    },
    mbtouchstart(e) {
      this.istouching = true;
      this.mbTouch.startposition.scrollTop = this.$refs.slist.scrollTop;
      this.mbTouch.startposition.clientY = e.targetTouches[0].clientY;
    },
    mbtouchmove(e) {
      if (this.mbTouch.startposition.scrollTop === 0) {
        let ymove = parseInt(
          e.targetTouches[0].clientY - this.mbTouch.startposition.clientY
        );
        if (ymove < 0) {
          return;
        }
        e.preventDefault();
        if (ymove > 24) {
          let znMv = this.damping(ymove, 500);
          this.topBoxtranslate = -this.$refs.tpbox.offsetHeight + (znMv + 21);
        } else {
          this.topBoxtranslate = -this.$refs.tpbox.offsetHeight + ymove;
        }
      }
    },
    mbtouchend(e) {
      this.istouching = false;
      if (this.mbTouch.startposition.scrollTop > 0) {
        return;
      }
      if (this.topBoxtranslate > -this.$refs.tpbox.offsetHeight + this.miniY) {
        this.topBoxtranslate = 0;
        this.curTab = 0;
      } else {
        this.topBoxtranslate = -this.$refs.tpbox.offsetHeight;
      }
    },
    mbscroll(e) {
      let selement = this.$refs.slist;
      if (
        selement.scrollHeight - selement.scrollTop <
          selement.clientHeight + 50 &&
        !this.freshing
      ) {
        this.onpullDown();
      }
    },
    onpullDown() {
      this.freshing = true;
      setTimeout(() => {
        let l = [];
        for (var i = this.slist.length; i < this.slist.length + 9; i++) {
          l.push({
            id: i,
          });
        }
        this.slist.push(...l);
        this.freshing = false;
      }, 2000);
    },
    damping(x, max) {
      let y = Math.abs(x);
      y = (0.82231 * max) / (1 + 4338.47 / Math.pow(y, 1.14791));
      return Math.round(x < 0 ? -y : y);
    },
  },
};
</script>

<style scoped lang="less">
.trans-enter-active,
.trans-leave-active {
  transition: transform 0.5s ease-out;
}

.trans-enter,
.trans-leave-to {
  transform: translate(0, -100%);
}

.arrowbox-enter,
.arrowbox-leave-to {
  height: 0 !important;
}

.arrowbox-enter-active,
.arrowbox-leave-active {
  transition: height 0.5s ease-out;

  .icon-tubiaozhizuo- {
    display: none;
  }
}

.myfade-enter,
.myfade-leave-to {
  opacity: 0;
}

.myfade-enter-active {
  transition: all 0.75s ease-out;
}

.myfade-leave-active {
  transition: all 0.15s;
}

.icon-tubiaozhizuo- {
  font-size: 30px;
  color: #fff;
  text-align: center;
  transition: transform 0.3s ease-out;
  position: absolute;

  &.tab0 {
    bottom: 50px;
    left: 50%;
    transform: translate(-50%, 0);
  }

  &.tab1 {
    bottom: 20px;
    left: 50%;
    transform: translate(-50%, 0);
    color: #2f3542;
  }
}

.sm-top-box {
  width: 100%;
  height: 100px;
  z-index: 20;
  background: linear-gradient(to bottom, #cc2b5e, #753a88);
  position: absolute;
  top: 0;
  color: #fff;
  font-size: 30px;
  text-align: center;
  line-height: 100px;
  letter-spacing: 15px;
  font-weight: 600;
}

.restrain {
  width: 100%;
  height: 100vh;
  position: relative;
  overflow: hidden;

  .top_floatBox {
    width: 100%;
    height: 100vh;
    overflow: hidden;
    position: relative;
    transition: transform 0.5s ease-out;
    background-size: 100%;
    background-color: #2f3542;
    color: #ccc;

    .container {
      padding: 0 10px;
      height: 100vh;
      font-size: 17px;
      line-height: 27px;
    }
  }

  .mainbox {
    box-sizing: border-box;
    width: 100vw;
    height: 100vh;
    position: relative;
    transition: all 0.5s ease-out;
    display: flex;
    flex-direction: column;
    padding-top: 5px;

    .arrowbox {
      height: 95px;
      position: relative;
      overflow: hidden;
      flex-shrink: 0;
    }

    .container {
      overflow: scroll;
      flex: 1;

      .refreshing {
        text-align: center;
        height: 40px;
        line-height: 40px;
      }

      .item {
        color: #333;
        text-align: center;
        line-height: 40px;
        font-weight: 600;
        font-size: 17px;
      }
    }
  }
}
</style>
