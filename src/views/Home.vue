<template>
  <div class="home">
    <!-- 顶部盒子 -->
    <transition name="trans">
      <div class="sm-top-box" v-if="curTab == 1"></div>
    </transition>
    <div class="restrain">
      <!--第一个盒子-->
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
        <!--箭头-->
        <transition name="myfade">
          <div
            v-if="curTab === 0"
            class="iconfont icon-tubiaozhizuo tab0"
            :style="{
              transform: `rotate(${
                topBoxtranslate < -this.miniY ? '180deg' : '0deg'
              })`,
            }"
          >
            ↓
          </div>
        </transition>
      </div>
      <!--第二个盒子-->
      <div
        class="mainbox scroll"
        :style="[
          { transform: `translate(0px,${topBoxtranslate}px)` },
          restrainTsStyle,
        ]"
        @touchstart.prevent="mbtouchstart"
        @touchmove.prevent="mbtouchmove"
        @touchend.prevent="mbtouchend"
      >
        <div class="container list" ref="slist">
          <div class="item" v-for="item in slist" :key="item.id">
            这是第{{ item.id }}条数据
          </div>
        </div>
        <!--箭头-->
        <transition name="myfade">
          <div
            class="iconfont icon-tubiaozhizuo tab1"
            v-if="curTab === 1"
            :style="{
              transform: `rotate(${
                this.topBoxtranslate >
                -this.$refs.tpbox.offsetHeight + this.miniY
                  ? '180deg'
                  : '0deg'
              })`,
            }"
          >
            ↓
          </div>
        </transition>
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
      miniY: 70,
      curTab: 0,
      slist: [],
    };
  },
  created() {
    for (var i = 0; i < 9999; i++) {
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
      console.log("tp y方向移动" + ymove + "px");
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
      console.log('初始',this.mbTouch.startposition.scrollTop);
    },
    mbtouchmove(e) {
      let movedY = ( this.mbTouch.startposition.clientY - e.targetTouches[0].clientY).toFixed(2);
   console.log('滚动了'+movedY)
   this.$refs.slist.scrollTop = this.mbTouch.startposition.scrollTop +  movedY;
      if (this.mbTouch.startposition.scrollTop == 0) {
        let ymove = parseInt(
          e.targetTouches[0].clientY - this.mbTouch.startposition.clientY
        );
        if (ymove < 0) {
          return;
        }
        console.log("mb y方向移动" + ymove + "px");
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

.myfade-enter-active,
.myfade-leave-active {
  transition: opacity 0.5s;
}

.myfade-enter,
.myfade-leave-active {
  opacity: 0;
}

.icon-tubiaozhizuo {
  position: absolute;
  font-size: 30px;
  color: #fff;
  width: 30px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  transition: transform 0.3s ease-out;

  &.tab0 {
    bottom: 100px;
    left: 50%;
    transform: translate(-50%, 0);
  }

  &.tab1 {
    top: 50px;
    left: 50%;
    transform: translate(-50%, 0);
  }
}

.sm-top-box {
  width: 100%;
  height: 100px;
  z-index: 20;
  background: #747d8c;
  position: absolute;
  top: 0;
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
    padding-top: 100px;
    width: 100vw;
    height: 100vh;
    position: relative;
    overflow-y: scroll;
    background-color: #e67e22;

    .list {
      height: 100%;
      overflow: auto;
    }
  }
}
</style>
