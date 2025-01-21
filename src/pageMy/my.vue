<template>
  <div class="main">
    <div class="title">我的</div>
    <div class="head">
      <img src="../static/icon/my/head.svg" alt="" class="head-img">
      <div class="name">{{ user1.userName ===undefined?"未登录":user1.userName }}</div>
    </div>
    <up-cell-group>

      <up-cell v-for="(item,index) in navList" :key="index" :title="item.label" :icon="item.icon" isLink @click="navTo(item.url)" >
      </up-cell>
    </up-cell-group>
    <nav-bottom :checkNav="1" ></nav-bottom>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {});
onMounted(() => {});
function goIndex(){
  util.relaunch("/pages/index/index")
}
//----------------------<yhm-用户名>----------------------
const user1 = uni.getStorageSync("user")

//----------------------<gndh-功能导航>----------------------
import user from "@/static/icon/my/user.svg"
import mariner from "@/static/icon/my/mariner.svg"
import harbour from "@/static/icon/my/harbour.svg"

const navList = ref([
  {
    label:"列表1",
    icon:mariner,
    url:"/pageMy/information"
  },  
  {
    label:"列表2",
    icon:harbour,
    url:"/pageMy/harbour"
  },
{
    label:"退出",
    icon:harbour,
    url:"/pageLogin/login"
  },
])

function navTo(url:string){
  if(url.includes('login')){
    uni.showModal({
      title:"确认退出？",
      success(res){
        if(res.confirm){
          uni.clearStorageSync()
          util.relaunch(url);
        }
      }
    })
    return
  }
  util.go(url)
}
</script>
<style scoped lang="scss">
.main {
  height: 100vh;
  padding: unset;
  .title {
    width: 100vw;
    height: calc(10vh);
    padding: 0 $uni-spacing-row-lg;
    box-sizing: border-box;
    background: #ECF2FFFF;
    display: flex;
    align-items:flex-end;
  }
  .head {
    width: 100vw;
    height:calc(16vh) ;
    background: $uni-bg-color-grey;
    display: flex;
    align-items: center;
    padding: 0 $uni-spacing-row-lg;
    box-sizing: border-box;
    background: #ECF2FFFF;
    .head-img {
      width: 60px;
      height: 60px;
      border-radius: 40px;
    }
    .name {
      margin-left: $uni-spacing-row-lg;
    }
  }
  .cell-group {
    box-sizing: border-box;
      padding: 0 $uni-spacing-row-lg;
    .cell {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 $uni-spacing-row-lg;
      height: 60px;
      border-bottom: 1px solid $uni-border-color;
      .cell-info {
        display: flex;
        align-items: center;
        .icon {
          width: 24px;
          height: 24px;
        }
        .title {
          margin-left: $uni-spacing-row-base;
        }
      }
      .right {
        .state {
          display: flex;
          align-items: center;
          justify-content: center;
          width: 70px;
          height: 30px;
          border-radius: 15px;
          background: $uni-bg-color-grey;
          font-size: 14px;
        }
      }
    }
  }
}
</style>
