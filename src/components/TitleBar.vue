<!-- 顶部标题栏 -->
<template>
  <transition name="slide-down">
      <div class="title-wrapper" v-show="ifTitleAndMenu">
        <div class="title-left">
          <span class="icon-cheveron-left icon"></span>
        </div>
        <div class="title-right">
          <div class="icon-wrapper">
            <span class="icon-shopping-cart icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-user-solid-square icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="icon-dots-horizontal-triple icon"></span>
          </div>
        </div>
      </div>
    </transition>
</template>

<script>
export default {
  props: {
      ifTitleAndMenu: {
          type: Boolean,
          default: false
      }
  }
}

</script>
<style lang='scss' scoped>
@import '../assets/styles/global';
.title-wrapper {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    width: 100%;
    height: px2rem(30);
    display: flex;
    background: white;
    box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.2);
    .title-left {
      flex: 0 0 px2rem(60);
      @include center;
    }
    .title-right {
      flex: 1;
      display: flex;
      justify-content: flex-end;
      .icon-wrapper {
        flex: 0 0 px2rem(40);
        @include center;
      }
    }
    &.slide-down-enter, &.slide-down-leave-to {
      transform: translate3d(0, -100%, 0);
    }
    &.slide-down-enter-to, &.slide-down-leave {
      transform: translate3d(0, 0, 0);
    }
    &.slide-down-enter-active, &.slide-down-leave-active {
      transition: all .3s linear;
    }
  }
</style>