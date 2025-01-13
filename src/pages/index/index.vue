<template>
  <div class="main">
    <div class="btns">
      <div class="btn" @click="goIn">
        <idv class="icon in">
          <img
            src="../../static/icon/common/anchor.svg"
            alt=""
            class="icon-img"
          />
        </idv>
        <div class="title">进港申报</div>
        <up-icon name="arrow-rightward"></up-icon>
      </div>
      <div class="btn" @click="goOut">
        <idv class="icon out">
          <img
            src="../../static/icon/common/anchor.svg"
            alt=""
            class="icon-img"
          />
        </idv>
        <div class="title">出港申报</div>
        <up-icon name="arrow-rightward"></up-icon>
      </div>
    </div>

    <div class="filter">
      <div class="title">
        <img src="../../static/icon/index/user.svg" alt="" class="icon" />
        <div class="text">申报记录</div>
      </div>
      <up-search
        shape="square"
        :showAction="false"
        placeholder="请输入船舶名称"
        v-model="keyWord"
      ></up-search>
    </div>

    <div class="list">
      <div class="empty" v-if="list.length==0" >
        <img src="../../static/icon/common/record.svg" alt="" class="empty-img">
          <div class="empty-title">暂无申报记录</div>
         
      </div>
      <div class="item" v-for="(item,index) in list" :key="index" @click="goDetail(item)">
        <div class="item-state in">{{item.sailingHarbourApplyType===0?'出港':'进港'}}申报</div>
        <div class="item-title">{{item.shipName}}</div>
        <div class="item-info">
          <div class="item-info-text">
            <img
              src="../../static/icon/index/mariner.svg"
              alt=""
              class="item-info-icon"
            />
            <div class="item-info-label">船员：</div>
            <div class="item-info-text">{{item.sailingHarbourPersonNum}}</div>
          </div>
          <div class="item-info-text">
            <img
              src="../../static/icon/index/harbour.svg"
              alt=""
              class="item-info-icon"
            />
            <div class="item-info-label">船籍港：{{item.cbShipVO.harbourName}}</div>
          </div>
        </div>
        <up-line></up-line>
        <div class="item-port-state">
          <div class="state"></div>
          <div class="item-port-text">{{item.sailingHarborApplyType==0?'出港':'进港'}}报备-{{initState(item.sailingHarbourAuditState)}}</div>
        </div>
        <div class="item-group">
          <div class="item-group-item">
            <img
              src="../../static/icon/index/anchor.svg"
              alt=""
              class="item-group-icon"
            />
            <div class="item-group-label">出港港口</div>
            <div class="item-group-text">{{item.harbourName}}</div>
          </div>
          <div class="item-group-item">
            <img
              src="../../static/icon/index/date.svg"
              alt=""
              class="item-group-icon"
            />
            <div class="item-group-label">计划出港时间</div>
            <div class="item-group-text time">{{item.plannedDate}}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="nav-bottom">
      <div class="item">
        <img src="../../static/icon/nav/send-active.svg" class="icon" alt="" />
        <div class="label">进出港申报</div>
      </div>

      <div class="item" @click="goMy">
        <img src="../../static/icon/nav/my.svg" class="icon" alt="" />
        <div class="label">我的</div>
      </div>
    </div>
    
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {});
onShow(()=>{
  console.log('1',1)
  getList()
})
onMounted(() => {

});
//----------------------<tzxq-跳转详情>----------------------
function goDetail(item:any){
  util.go("/pageIndex/submit?record="+JSON.stringify(item)+"&isEdit=true")
}
//----------------------<yhxx-用户信息>----------------------
const user = uni.getStorageSync("user")

//----------------------<tzwd-跳转我的>----------------------
function goMy(){
  util.relaunch("/pageMy/my")
}
//----------------------<tzsb-跳转申报>----------------------
function goIn(){
  util.go("/pageIndex/submit?inOut=false")
}
function goOut(){
  util.go("/pageIndex/submit?inOut=true")
}
//----------------------<sbjl-申报记录>----------------------
function initState(state:number){
  switch(state){
    case 0:return '待审核';
    case 1:return '审核中';
    case 2:return '驳回';
    case 3:return '自动作废';
    case 4:return '缺失纠正';
    case 5:return '违规记录';
    case 90:return '审核通过';
  }
}
const list = ref([])
function getList(){
  if(user==""||user==null) {
  util.relaunch("/pageLogin/login")
  }
  request('/fisherApi/wechatMiniProgram/findCbShipEnterOuterHarbourByLoginName',{},'POST').then(res=>{
  console.log('res',res)
    list.value = res
  }).catch(err=>{
  console.log('err',err)
  })
  
}
</script>
<style scoped lang="scss">
.main {
  background: $uni-bg-color-grey;
  .btns {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-column-gap: 16px;
    padding: 0 16px;
    margin-bottom: 10px;

    .btn {
      display: flex;
      align-items: center;
      justify-content: space-around;
      width: 100%;
      height: 80px;
      background: white;
      border-radius: 5px;
      padding: 0 10px;
      box-sizing: border-box;

      .icon {
        width: 36px;
        height: 36px;
        border-radius: 5px;
        display: flex;
        align-items: center;
        justify-content: center;
        &.in {
          background: $uni-color-zt-1;
        }
        &.out {
          background: $uni-color-zt-2;
        }
        .icon-img {
          width: 30px;
          height: 30px;
        }
      }
      .title {
        font-size: 16px;
      }
    }
  }

  .filter {
    background: white;
    padding: 16px;
    margin-bottom: 10px;
    .title {
      display: flex;
      margin-bottom: 10px;
      .icon {
        width: 16px;
        height: 16px;
      }
      .text {
        margin-left: 10px;
      }
    }
  }

  .list {
    width: 100vw;
    height: calc(100vh - 88px - 80px - 110px - 90px);
    overflow-y: auto;
    .item {
      width: 100%;
      position: relative;
      padding: 16px;
      box-sizing: border-box;
      background: white;
      margin-bottom: $uni-spacing-row-base;
      font-size: 12px;
      .item-state {
        position: absolute;
        right: 5px;
        top: 5px;
        width: 65px;
        height: 27px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 14px;
        color: white;
        &.in {
          background: $uni-color-zt-1;
        }
        &.out {
          background: $uni-color-zt-2;
        }
      }
      .item-title {
        font-size: 14px;
        font-weight: bolder;
        margin-bottom: $uni-spacing-row-base;
      }
      .item-info {
        display: flex;
        margin-bottom: $uni-spacing-row-sm;
        padding-left: 3px;
        box-sizing: border-box;
        .item-info-text {
          display: flex;
          margin-right: 20px;
          color: #666666ff;

          .item-info-icon {
            width: 12px;
            height: 12px;
          }

          .item-info-label {
            color: $uni-text-color-grey;
            margin-left: 10px;
          }
          .item-info-text {
            color: $uni-color-primary;
          }
        }
      }
      .item-port-state {
        display: flex;
        align-items: center;
        margin: $uni-spacing-row-base 0;
        .state {
          width: 10px;
          height: 10px;
          border-radius: 5px;
          background: $uni-color-primary;
          margin-left: 3px;
          &.state1 {
            //草稿
            background: #949494ff;
          }
          &.state2 {
            //通过
            background: #3694ffff;
          }
          &.state3 {
            //申报
            background: #ff8d1aff;
          }
        }
        .item-port-text {
          margin-left: 13px;
          color: #949494ff;
        }
      }
      .item-group {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 12px;
      .time {
            font-size: 10px;
          }
        .item-group-item {
          display: flex;
          .item-group-icon {
            width: 16px;
            height: 16px;
          }
          .item-group-label {
            color: $uni-text-color-grey;
            margin-left: 10px;
          }
          .item-group-text {
            margin-left: 10px;
            color: black;
          }
          
        }
      }
    }
  }
}
</style>
