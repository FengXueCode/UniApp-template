<template>
  <div class="main">
    <up-navbar :autoBack="true" title="船舶绑定">
    </up-navbar>
    <div class="ship-list">
      <div class="empty" v-if="list.length==0" >
        <img src="../static/icon/common/record.svg" alt="" class="empty-img">
          <div class="empty-title">暂无船舶</div>
         
      </div>
      <div class="item" @click="goDetail(item)" v-for="(item, index) in list" :key="index">
        <div class="item-name">{{initNull( item.shipName)}}</div>
        <div class="item-state">{{initShipType(item.shipType)}}</div>
        <up-line color="#F5F5F5FF"></up-line>
        <div class="item-list">
          <div class="item-info">
            <img src="../static/icon/my/board.svg" alt="" class="icon" />
            <div class="text">船牌：{{initNull(item.shipPlateNo)}}</div>
          </div>
          <div class="item-info">
            <img src="../static/icon/index/mariner.svg" alt="" class="icon" />
            <div class="text">归属渔港：{{initNull(item.harbourName)}}</div>
          </div>
          <div class="item-info">
            <img src="../static/icon/my/line.svg" alt="" class="icon" />
            <div class="text">北斗卡号：{{initNull(item.terminalBdIc)}}</div>
          </div>
          <div class="item-info">
            <img src="../static/icon/my/mmsi.svg" alt="" class="icon" />
            <div class="text">MMSI：{{initNull(item.mmsi)}}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="bottom-btn">
      <div class="btn" @click="goAdd" >
        <up-icon name="plus" color="white"></up-icon>
        <div class="text">
        新增船舶
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {
  uni.hideHomeButton();
});
onShow(()=>{
  getList()
})
onMounted(() => {});
function goDetail(item:any){
  util.go("/pageMy/detail?item="+JSON.stringify(item))
}
const initNull = util.initNull
//----------------------<cylb-船员列表>----------------------
const user = uni.getStorageSync("user")
const list = ref([])
function getList(){
  request('/fisherApi/wechatMiniProgram/findShipByUserId',{},'POST').then(res=>{
  console.log('res',res)
    list.value = res
  }).catch(err=>{
  console.log('err',err)
  })
}
//----------------------<cshlx-初始化类型>----------------------
const typeList = config.shipType
function initShipType(num:number){
  let item = typeList.find(element=>{
    return element.value == num
  })

  return item==undefined?"":item.label
}
//----------------------<tzxz-跳转选择>----------------------
function goAdd(){
  util.go("/pageMy/add")
}
</script>
<style scoped lang="scss">
.main {
  .title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 60px;
    background: white;
    padding: 0 $uni-spacing-row-lg;
    box-sizing: border-box;
    .info {
    }
    .btn {
      width: 80px;
      height: 40px;
      background: $uni-bg-color-grey;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
  .ship-list {
    background: $uni-bg-color-grey;
    height: calc(100vh - 60px - 40px - 80px);
    width: 100vw;
    padding: 10px $uni-spacing;
    box-sizing: border-box;
    overflow-y: auto;
    .item {
      width: 100%;
      background: white;
      padding: $uni-spacing;
      box-sizing: border-box;
      position: relative;
      font-size: 14px;
      margin-bottom: 10px;
      .item-name {
        margin-bottom: 10px;
        font-size: 12px;
      }
      .item-state {
        position: absolute;
        right: 5px;
        top: 5px;
        background: $uni-color-zt-1;
        height: 27px;
        width: 65px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: white;

      }
      .item-list {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        grid-row-gap: 10px;
        margin-top: 10px;
        font-size: 10px;
        .item-info {
          display: flex;
          .icon {
            width: 16px;
            height: 16px;
          }
          .text {
            margin-left: 10px;
          }
        }
      }
    }
  }
  .bottom-btn {
    .btn {
      .text {
        margin-left: 10px;
      } 
    }
  }
}
</style>
