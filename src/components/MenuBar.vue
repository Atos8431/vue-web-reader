<template>
  <div class="menu-bar">
    <!-- 底部菜单栏 -->
    <transition name="slide-up">
      <div
        class="menu-wrapper"
        v-show="this.ifMenuShow"
        :class="{ 'hide-box-shadow': ifFontSizeShow || !ifMenuShow }"
      >
        <div class="icon-wrapper">
          <span class="icon-list icon" @click="showFontSize(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon" @click="showFontSize(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-brightness icon" @click="showFontSize(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-font icon" @click="showFontSize(0)"></span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="this.ifFontSizeShow">
        <div class="setting-font-size" v-if="showTag === 0">
          <div
            class="preview-left"
            :style="{ fontSize: fontSizeList[0].fontSize + 'px' }"
          >
            A
          </div>
          <div class="select">
            <div
              class="select-wrapper"
              v-for="(item, index) in fontSizeList"
              :key="index"
              @click="setFontSize(item.fontSize)"
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defalutFontSize === item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div
            class="preview-right"
            :style="{
              fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px',
            }"
          >
            A
          </div>
        </div>
        <div class="setting-theme" v-else-if="showTag === 1">
          <div
            class="setting-theme-item"
            v-for="(item, index) in themeList"
            :key="index"
            @click="setTheme(index)"
          >
            <div
              class="preview"
              :style="{ background: item.style.body.background }"
              :class="{ 'no-border': item.style.body.background != '#fff' }"
            ></div>
            <div class="text" :class="{ selected: index === defaultTheme }">
              {{ item.name }}
            </div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <input
              class="progress"
              type="range"
              max="100"
              min="0"
              step="1"
              @change="onProgressChange($event.target.value)"
              @input="onProgressInput($event.target.value)"
              :value="progress"
              :disabled="!bookAvailable"
              ref="progress"
            />
          </div>
          <div class="text-wrapper">
            <span>{{ bookAvailable ? progress + "%" : "加载中..." }}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view
      :ifShowContent="ifShowContent"
      v-show="ifShowContent"
      :navigation="navigation"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
    ></content-view>
    <transition class="fade">
      <div
        class="content-mask"
        v-show="ifShowContent"
        @click="hideContent"
      ></div>
    </transition>
  </div>
</template>

<script>
import ContentView from "@/components/Content";
export default {
  props: {
    ifMenuShow: {
      type: Boolean,
      default: false,
    },
    fontSizeList: {
      type: Array,
    },
    defalutFontSize: {
      type: Number,
    },
    themeList: {
      type: Array,
    },
    defaultTheme: {
      type: Number,
    },
    bookAvailable: {
      type: Boolean,
    },
    navigation: {
      type: Object,
    }
  },
  data() {
    return {
      ifFontSizeShow: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false,
    };
  },
  methods: {
    showFontSize(showTag) {
      if (showTag === 3) {
        this.ifFontSizeShow = false;
        this.ifShowContent = true;
      } else {
        if (this.ifFontSizeShow && this.showTag === showTag) {
          this.ifFontSizeShow = false;
        } else {
          this.ifFontSizeShow = true;
          this.showTag = showTag;
        }
      }
    },
    hideFontSize() {
      this.ifFontSizeShow = false;
    },
    setFontSize(fontSize) {
      this.$emit("setFontSize", fontSize);
    },
    setTheme(index) {
      this.$emit("setTheme", index);
    },
    onProgressInput(progress) {
      this.progress = progress;
      this.$ref.progress.style.backgroundSize = `$(this.progress)% 100%`;
    },
    onProgressChange(progress) {
      this.$emit("onProgressChange", progress);
    },
    hideContent() {
      this.ifShowContent = false;
    },
    jumpTo(target){
      this.$emit('jumpTo', target)
    }
  },
  components: {
    ContentView,
  },
};
</script>

<style lang="scss" scoped>
@import "../assets/styles/global";
.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100%;
    height: px2rem(48);
    z-index: 101;
    display: flex;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    background: white;
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(24);
      }
      .icon-brightness {
        font-size: px2rem(24);
      }
      .icon-font {
        font-size: px2rem(19);
      }
    }
  }
  .setting-wrapper {
    position: absolute;
    bottom: px2rem(48);
    z-index: 101;
    left: 0;
    width: 100%;
    height: px2rem(60);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .setting-font-size {
      display: flex;
      height: 100%;
      .preview-left {
        position: absolute;
        margin-left: px2rem(40);
        padding-right: 0;
        padding-top: px2rem(24);
      }
      .preview-right {
        margin-right: px2rem(40);
        flex: 0 0 px2rem(40);
        display: flex;
        align-items: center;
      }
      .select {
        display: flex;
        flex: 1;
        .select-wrapper {
          flex: 1;
          display: flex;
          @include center;
          &:first-child {
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid rgb(105, 102, 102);
          }
          .point-wrapper {
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid rgb(105, 102, 102);
            position: relative;
            .point {
              position: absolute;
              top: px2rem(-8);
              left: px2rem(-10);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              background: white;
              border: px2rem(1) solid #ccc;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
              @include center;
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                background: black;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
    .setting-theme {
      height: 100%;
      display: flex;
      .setting-theme-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview {
          flex: 1;
          border: px2rem(1) solid #ccc;
          box-sizing: border-box;
          &.no-border {
            border: none;
          }
        }
        .text {
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #999;
          @include center;
          &.selected {
            color: #333;
          }
        }
      }
    }
  }
  .setting-progress {
    position: relative;
    width: 100%;
    height: 100%;
    .progress-wrapper {
      width: 100%;
      height: 100%;
      @include center;
      padding: 0 px2rem(30);
      box-sizing: border-box;
      .progress {
        width: 100%;
        -webkit-appearance: none; // 覆盖默认样式
        height: px2rem(2);
        background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
        background-size: 0 100%;
        &:focus {
          outline: none;
        }
        &::-webkit-slider-thumb {
          -webkit-appearance: none;
          height: px2rem(20);
          width: px2rem(20);
          border-radius: 50%;
          background: white;
          box-shadow: 0 4px 4px rgba(0, 0, 0, 0.15);
          border: px2rem(1) solid #ddd;
        }
      }
    }
    .text-wrapper {
      position: absolute;
      left: 0;
      bottom: 0;
      width: 100%;
      color: #333;
      font-size: px2rem(12);
      text-align: center;
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, 0.8);
  }
}
</style>
