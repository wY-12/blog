<template>
  <div
    class="home-container"
    ref="container"
    @wheel="handlewheel"
    v-loading="isLoading"
  >
    <ul
      class="carousel-container"
      :style="{ marginTop }"
      @transitionend="handleTransitionEnd"
    >
      <li v-for="item in data" :key="item.id">
        <CarouselItem :carousel="item" />
      </li>
    </ul>
    <div class="icon icon-up" @click="switchTo(index - 1)" v-show="index >= 1">
      <Icon type="arrowUp" />
    </div>
    <div
      class="icon icon-down"
      @click="switchTo(index + 1)"
      v-show="index < data.length - 1"
    >
      <Icon type="arrowDown" />
    </div>
    <ul class="indicator">
      <li
        v-for="(item, i) in data"
        :key="item.id"
        @click="switchTo(i)"
        :class="{
          active: i === index,
        }"
      ></li>
    </ul>
  </div>
</template>

<script>
import Icon from "@/Components/Icon";
import CarouselItem from "./Carouselitem";
import { getBanners } from "@/api/banner.js";
import fetchData from "@/mixins/fetchData";
export default {
  mixins: [fetchData([])],
  components: {
    Icon,
    CarouselItem,
  },
  data() {
    return {
      index: 0,
      containerHeight: 0,
      switching: false, //是否正在切换
    };
  },

  mounted() {
    this.containerHeight = this.$refs.container.clientHeight;
    window.addEventListener("resize", this.handleResize);
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  computed: {
    marginTop() {
      return -this.index * this.containerHeight + "px";
    },
  },
  methods: {
    async fetchData() {
      return await getBanners();
    },
    switchTo(i) {
      this.index = i;
    },
    handlewheel(e) {
      if (this.switching) {
        return;
      }
      if (e.deltaY >= 125 && this.index < this.data.length - 1) {
        this.switching = true;

        this.index++;
      }
      if (e.deltaY <= -125 && this.index > 0) {
        this.switching = true;

        this.index--;
      }
    },
    handleTransitionEnd() {
      this.switching = false;
    },
    handleResize() {
      this.containerHeight = this.$refs.container.clientHeight;
    },
  },
};
</script>

<style scoped lang="less">
@import "~@/styles/var.less";
@import "~@/styles/mixin.less";
.home-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }
}
//测试

.carousel-container {
  width: 100%;
  height: 100%;
  transition: 500ms;
  li {
    width: 100%;
    height: 100%;
  }
}
.icon {
  .self-center();
  font-size: 30px;
  color: @gray;
  transform: translateX(-50%);

  cursor: pointer;
  &.icon-up {
    top: 25px;
    animation: jump-up 2s infinite;
  }
  &.icon-down {
    top: auto;
    bottom: 25px;
    animation: jump-down 2s infinite;
  }
  @jump: 5px;
  @keyframes jump-up {
    0% {
      transform: translate(-50%, @jump);
    }
    50% {
      transform: translate(-50%, -@jump);
    }
    100% {
      transform: translate(-50%, @jump);
    }
  }
  @keyframes jump-down {
    0% {
      transform: translate(-50%, -@jump);
    }
    50% {
      transform: translate(-50%, @jump);
    }
    100% {
      transform: translate(-50%, -@jump);
    }
  }
}
.indicator {
  .self-center();

  transform: translateY(-50%);
  right: 20px;
  left: auto;
  li {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: @words;
    cursor: pointer;
    margin-bottom: 10px;
    border: 1px solid #fff;
    box-sizing: border-box;
  }
  .active {
    background: #fff;
  }
}
</style>