<template>
  <div class="ebook">
    <title-bar :ifMenuShow="ifMenuShow"></title-bar>
    <div class="read-wrapper">
      <!-- 这是用来展示epub电子书 -->
      <div id="read"></div>
      <!-- 这是浮层，用来实现翻页 -->
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :ifMenuShow="ifMenuShow"
      :fontSizeList="fontSizeList"
      :defalutFontSize="defalutFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onProgressChange="onProgressChange"
      :navigation="navigation"
      @jumpTo="jumpTo"
      ref="menuBar"
     ></menu-bar>
  </div>
</template>

<script>
import TitleBar from "@/components/TitleBar.vue";
import MenuBar from "@/components/MenuBar.vue";
import Epub from "epubjs";
const DOWNLOAD_URL = "/static/SherlockHolmes.epub";
export default {
  data() {
    return {
      ifMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 },
      ],
      defalutFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000', 'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000', 'background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff', 'background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#000', 'background': 'rgb(241, 236, 226)'
            }
          }
        },
      ],
      defaultTheme: 0,//  用户选择了某一主题后，我们可以保存下来，下次用户再次登录的时候读取
      bookAvailable: false,
    };
  },
  methods: {
    showEpub() {
      //  生成epub对象
      this.book = new Epub(DOWNLOAD_URL);
      //  console.log(this.book)
      //  生成Rendition，通过Book.renderTo
      //  将解析后的数据挂载到readDOM对象上 --》 在这里表示为id="read"的dev元素
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight,
      });
      //  通过Rendition.display渲染电子书
      this.rendition.display();
      //  获取一个Theme对象
      this.themes = this.rendition.themes
      //  设置默认字体
      this.setFontSize(this.defalutFontSize)
      //  this.themes.register(name, styles) 通过所设置的参数注册主题
      this.registerTheme()
      //  this.themes.select(name) 通过主题名称可以切换主题
      this.setTheme(this.defaultTheme)
      //  获取location对象 默认不会生成，比较消耗性能
      //  通过epubjs的钩子函数来实现
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        console.log(this.navigation)
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        this.bookAvailable = true
      })

    },
    prevPage() {
      //  返回上一页
      if (this.rendition) {
        this.rendition.prev();
      }
    },
    nextPage() {
      //  到下一页
      if (this.rendition) {
        this.rendition.next();
      }
    },
    toggleMenu() {
      this.ifMenuShow = !this.ifMenuShow;
      if (!this.ifMenuShow) {
        this.$refs.menuBar.hideFontSize();
      }
    },
    setFontSize(fontSize){
      this.defalutFontSize = fontSize
      if(this.themes){
        this.themes.fontSize(fontSize + 'px')
      }
    },
    registerTheme(){
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setTheme(index){
      this.defaultTheme = index
      this.themes.select(this.themeList[index].name)
    },
    onProgressChange(progress){
      //  progress进度条的数值(0~100)
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    //  根据链接跳转到指定方法
    jumpTo(href){
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu(){
      //  隐藏标题栏和菜单栏
      this.ifMenuShow = false
      //  隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideFontSize()
      //  隐藏目录
      this.$refs.menuBar.hideContent()
    }
  },
  mounted() {
    this.showEpub();
  },
  components: {
    TitleBar,
    MenuBar,
  },
};
</script>

<style lang="scss" scoped>
@import "assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(300);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(300);
      }
    }
  }
}
</style>
