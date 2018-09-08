<!-- book的组件 -->
<template>
  <div class="ebook">
    <title-bar
    :ifTitleAndMenu="ifTitleAndMenu"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" v-on:click="prevPage"></div>
        <div class="center" v-on:click="toggleTitleAndMenu"></div>
        <div class="right" v-on:click="nextPage"></div>
      </div>
      
    </div>
    <menu-bar
    :ifTitleAndMenu="ifTitleAndMenu"
    :fontSizeList="fontSizeList"
    :defaultFontSize="defaultFontSize"
    @setFontSize="setFontSize"
    :themeList="themeList"
    :defaultTheme="defaultTheme"
    @setTheme="setTheme"
    :bookAvailable="bookAvailable"
    @onProgressChange="onProgressChange"
    :navigation="navigation"
    @jumpTo="jumpTo"
    ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import Epub from 'epubjs'
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
const DOWNLOAD_URL = './static/大江东去.epub'
global.ePub = Epub
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data() {
    return {
      ifTitleAndMenu: false,
      fontSizeList: [
        { fontSize: 12},
        { fontSize: 14},
        { fontSize: 16},
        { fontSize: 18},
        { fontSize: 20},
        { fontSize: 22},
        { fontSize: 24},
      ],
      defaultFontSize: 16,
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
              'color': '#000', 'background': '#cce'
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
          name: 'bold',
          style: {
            body: {
              'color': '#000', 'background': 'rgb(241, 234, 226)'
            }
          }
        }
      ],
      defaultTheme: 0,
      bookAvailable: false,
      navigation: {}
    }
  },
  methods: {
    //  根据链接跳转
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      this.ifTitleAndMenu = false
      this.$refs.menuBar.hideSetting()
      this.$refs.menuBar.hideContent()
    },
    //progress 数值[0-100]
    onProgressChange(progress){
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    registerTheme(){
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize
      if(this.themes){
        this.themes.fontSize(fontSize + 'px')
      }
    },
    //切换菜单和标题的显示状态
    toggleTitleAndMenu() {
      this.ifTitleAndMenu = !this.ifTitleAndMenu
      if(!this.ifTitleAndMenu){
        this.$refs.menuBar.hideSetting()
      }
    },
    //上一页
    prevPage() {
      //通过rendition对象
      if(this.rendition){
        this.rendition.prev()
      }
    },
    //下一页
    nextPage() {
      if(this.rendition){
        this.rendition.next()
      }
    },
    //电子书的解析和渲染
    showEpub() {
      //生成book
      this.book = new Epub(DOWNLOAD_URL)
      // console.log(this.book)
      //生成rendition对象
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      //display渲染电子书
      this.rendition.display()
      //获取theme对象
      this.themes = this.rendition.themes
      //设置默认字体
      this.setFontSize(this.defaultFontSize)
      //theme
      this.registerTheme()
      this.themes.select(this.defaultTheme)
      //获取locations对象
      //通过钩子函数实现
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        console.log(navigator)
        return this.book.locations.generate()
      }).then(result => {
        // console.log(result)
        this.locations = this.book.locations
        this.bookAvailable = true
        // this.onProgressChange(100)
      })
    }
      
  },
  //构造方法
  mounted() {
    this.showEpub()
  }
}

</script>
<style lang='scss' scoped>
@import 'assets/styles/global';
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
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
  
}
</style>