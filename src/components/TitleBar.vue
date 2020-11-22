<template>
  <div>
    <!-- 标题菜单栏 -->
    <transition name="slide-down">
      <div class="title-wrapper" v-show="this.ifMenuShow">
        <div class="left">
          <span class="icon-back icon"></span>
        </div>
        <div class="right">
          <div class="icon-wrapper">
            <span class="icon-shooping-car icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-user icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-other icon"></span>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    ifMenuShow: {
      type: Boolean,
      default: false,
    },
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/styles/global";
.title-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  height: px2rem(48);
  z-index: 101;
  display: flex;
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
  background: white;
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    display: flex;
    justify-content: flex-end; //  居右排列
    .icon-wrapper {
      flex: 0 0 px2rem(40);
      @include center;
      .icon-shooping-car {
        font-size: px2rem(23);
      }
    }
  }
}
</style>
