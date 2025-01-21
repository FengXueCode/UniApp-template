<template>
  <div class="main">
    <up-navbar title="首页"  leftIcon=""></up-navbar>
    <div class="content">
      <up-cell-group>
      <up-cell v-for="(item,index) in navList" :key="index" :title="item.label"  isLink  :url="item.url" >
      </up-cell>
    </up-cell-group> 
    </div>
    <nav-bottom :checkNav="0" ></nav-bottom>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {});
onShow(()=>{
})
onMounted(() => {

});
 const navList = ref([
  {
    label:"表单模板",
    url:"/pageIndex/formTemplate"
  },  
 {
    label:"地图模板",
    url:"/pageIndex/mapTemplate"
  }
])
</script>
<style scoped lang="scss">
.main {
  .content {
    height: calc(100% - 88px);
  }

}
</style>
