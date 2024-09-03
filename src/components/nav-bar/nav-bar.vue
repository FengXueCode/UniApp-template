<!--
 * @Author: FengXue
 * @Date: 2023-12-01 10:59:03
 * @LastEditors: FengXue
 * @LastEditTime: 2024-06-21 11:04:43
 * @filePath: Do not edit
-->
<template>
  <div class="nav" >
    <div class="icon" v-if="flag" @click="goBack">
      <up-icon name="arrow-left"></up-icon>
    </div>
    <div class="title">
      <slot></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
const props = defineProps({
  show: {
    type: Boolean,
    default: false,
  },
});

let flag = ref(true);
let pages = getCurrentPages();
let currentRoute = pages[pages.length - 1].route;
if (currentRoute == "pages/index/index" || currentRoute == "pages/user/user") {
  flag.value = false;
}
const goBack = () => {
  let canNavBack = getCurrentPages();
  console.log('canNavBack',canNavBack.length)
  if (canNavBack && canNavBack.length > 1) {
    uni.navigateBack({
      delta: 1,
    });
  } else {
    history.back();
  }
};
</script>
<style lang="scss" scoped>
@import "../../static/css/style.scss";
@import "../../static/css/unit.scss";
.nav {
  width: 100vw;
  height: $nav-height;
  position: fixed;
  top: 0;
  z-index: 99999;
  box-sizing: border-box;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  padding-bottom: 10px;
  padding-right: 10px;
  
  .icon {
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 99999;
    bottom: 10px;
    margin-right: 10px;
  }
  .title {
    width: 100%;
    text-align: center;
    position: absolute;
    bottom: 10px;
    font-size: 16px;
  }
  .right-icon {
    width: 30px;
    height: 30px;
    
  }
  
}
.arrow-left {
  width: 24px;
  height: 24px;
}
</style>
