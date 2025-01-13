<template>
  <div class='main'>
    <div class="content">
    <up-navbar title="船员管理" :autoBack="true"></up-navbar>

    <up-search
        shape="square"
        :showAction="false"
        placeholder="请输入"
      ></up-search>

    <div class="list">
<div class="empty"  v-if="list.length==0" >
            <up-empty mode="list" text="暂无船员"></up-empty>
      </div>
        <up-swipe-action>
          <up-swipe-action-item
            :options="option"
            v-for="item in list"
            :closeOnClick="true"
            :key="item.cbCrewId"
            @click="remove(item)"
          >
            
        <div class="item" >
          <div class="head">
            <div class="head-img-back">
            <img src="../static/icon/my/harbour-user.svg" alt="" class="icon">
            </div>
            <div class="head-title">{{item.crewName}}</div>
          </div>
          <div class="info">
            <div class="info-text">户籍：{{item.crewRegister}}</div>
            <div class="info-text">联系方式：{{item.crewPhone}}</div>
          </div>
        <div class="info">
            <div class="info-text">现居住地址：{{item.crewCurrentResidence}}</div>
          </div>
        </div>
          </up-swipe-action-item>
        </up-swipe-action>

      </div>
    </div>

    <div class="bottom-btn">
      <div class="btn" @click="add">新增船员</div>
    </div>

    <up-popup :show="showAdd" mode="bottom" :round="10" >
      <div class="popup">
        <div class="popup-head">
          <up-icon crewName="arrow-left" @click="showAdd = false"></up-icon>
          <div class="text">新增船员</div>
          <!-- <up-icon crewName="close"></up-icon> -->
        </div>

<up-form :model="harbourForm" ref="form" :rules="rules" labelAlign="right">
<up-form-item label="船舶" prop="harbourNo" labelWidth="80">
            <div
              class="select"
              :class="{ 'no-input': harbourForm.cbShipId == '' }"
              @click="showShip = true"
            >
              <div class="text">
              {{ harbourForm.cbShipId == "" ? "请选择船舶" : ship.shipName }}
              </div>

              <up-icon name="arrow-right"></up-icon>
            </div>

            <up-picker
              @cancel="showShip = false"
              confirmColor="#07C160"
              :show="showShip"
              :columns="[shipList]"
              keyName="shipName"
              @confirm="sureShip"
            ></up-picker>
          </up-form-item>
        <up-form-item label="姓名" prop="crewName" labelWidth="80">
          <up-input
            placeholder="请输入姓名"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="harbourForm.crewName"
          ></up-input>
        </up-form-item>

        <up-form-item label="联系方式" prop="crewPhone" labelWidth="80">
          <up-input
            placeholder="请输入联系方式"
            type="text"
            maxlength="11"
            border="bottom"
            v-model="harbourForm.crewPhone"
          ></up-input>
        </up-form-item>
        <up-form-item
            label="身份证号"
            prop="crewIdCard"
            labelWidth="80"
          >
            <up-input
              placeholder="请输入身份证号"
              type="idcard"
              maxlength="18"
              border="bottom"
              v-model="harbourForm.crewIdCard"
            ></up-input>
          </up-form-item>
        <up-form-item label="户籍" prop="crewRegister" labelWidth="80">
          <up-input
            placeholder="请输入户籍所在地"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="harbourForm.crewRegister"
          ></up-input>
        </up-form-item>
      <up-form-item label="现居住地址" prop="crewCurrentResidence" labelWidth="80">
          <up-input
            placeholder="请输入现居住地址"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="harbourForm.crewCurrentResidence"
          ></up-input>
        </up-form-item>
      </up-form>
        <div class="btn-full" @click="sureAdd">确认</div>
      </div>
    </up-popup>
  </div>
</template>
<script setup lang='ts'>
onLoad((val:any)=>{

});
onShow(()=>{
  getList()
  getShipList()
})
onMounted(()=>{

});
//----------------------<cb-船舶>----------------------
const showShip = ref(false)
const shipList = ref([])
const ship = ref({shipName:""})
function getShipList(){
  request('/fisherApi/wechatMiniProgram/findShipByUserId',{},'POST').then(res=>{
  console.log('res',res)
    shipList.value = res
  }).catch(err=>{
  console.log('err',err)
  })
  
}
function sureShip(e){
  console.log('e',e)
  showShip.value = false
  ship.value = e.value[0]
  harbourForm.value.cbShipId = e.value[0].cbShipId
}
//----------------------<sccy-删除船员>----------------------
const option = ref([
  {
    text: "删除",
    style: {
      backgroundColor: "#f56c6c",
    },
  },
]);

function remove(item:any){
  uni.showModal({
    title:"确认删除？",
    success(res){
      if(res.confirm){
        request('/fisherApi/wechatMiniProgram/deleteCrewById',[ {cbCrewId:item.cbCrewId} ],'POST').then(res=>{
        console.log('res',res)
          util.toast("删除成功")
          getList()
        }).catch(err=>{
        console.log('err',err)
        })
        
      }
    }
  })
}
//----------------------<xzcy-新增船员>----------------------
function add(){
  showAdd.value = true
}

const showAdd = ref(false)
const form = ref()
const harbourForm = ref({
  crewName:"",
  crewPhone:"",
  crewRegister:"",
  crewCurrentResidence:"",
  cbShipId:""
})
const rules = ref({
  crewName: [
    {
      type: "string",
      required: true,
      message: "请输入姓名",
      trigger: ["blur", "change"],
    },
    {
      pattern: /^\S+$/,
      message: "不能存在空格",
    },
  ],
  crewPhone: [
    {
      required: true,
      message: "请输入手机号",
      trigger: ["blur", "change"],
    },
    {
      pattern: /^1[3-9]\d{9}$/,
      message: "请输入正确的手机号",
    },
  ],
  crewRegister: [
    {
      type:"string",
      required: true,
      message: "请输入户籍所在地",
      trigger: ["blur", "change"],
    }
  ],
 crewCurrentResidence: [
    {
      type:"string",
      required: true,
      message: "请输入现居住地址",
      trigger: ["blur", "change"],
    }
  ],
crewIdCard: [
    {
      required: true,
      message: "请输入身份证号",
      trigger: ["blur", "change"],
    },
    {
      pattern:
        /^[1-9]\d{5}(18|19|20)\d{2}(0[1-9]|1[0-2])(0[1-9]|[12]\d|3[01])\d{3}(\d|X)$/,
      message: "请输入正确的身份证",
    },
  ],
})
function sureAdd(){
  showAdd.value = false
  request('/fisherApi/wechatMiniProgram/addCrew',[{...harbourForm.value}],'POST').then(res=>{
  console.log('res',res)
    getList()
  }).catch(err=>{
  console.log('err',err)
  })
  
}
//----------------------<hqcy-获取船员>----------------------
const list = ref([])
const user = uni.getStorageSync("user")
function getList(isMore:boolean = false){
  request('/fisherApi/wechatMiniProgram/findCrewByUserId',{},'POST').then(res=>{
  console.log('res',res)
    list.value = res
  }).catch(err=>{
  console.log('err',err)
  })
  
}


</script>
<style scoped lang='scss'>
.content {
  width: 100%;
  height: calc(100% - 80px);
  .list {
    width: 100%;
    height: calc(100% - 50px);
    box-sizing: border-box;
    overflow-y: auto;
    margin-top: 10px;
    .item {
      padding: 10px ;
      box-sizing: border-box;
      border-bottom: 1px solid $uni-border-color;
      
      .head{
        display: flex;
        align-items: center;
        margin-bottom: 10px;
        .head-img-back{
          background: #0086FDFF;
          width: 30px;
          height: 30px;
          border-radius: 15px;
          display: flex;
          align-items: center;
          justify-content: center;
          .icon {
            width: 18px;
            height: 18px;
          }
        }
        .head-title{
          margin-left: 30px;
          font-size: 17px;
        }

      } 
      .info {
        display: flex;
        justify-content: space-between;
        align-items: center;
        color: #999999FF;
        font-size: 12px;
        margin-bottom: 5px;

      }
    }
  }
}
.popup{
  width: 100vw;
  height: 50vh;
  padding: 16px;
  box-sizing: border-box;
  .popup-head{
    display: flex;
    align-items: center;
    justify-content: space-between;
    .text {
      flex-grow: 1;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
  .btn-full {
    margin-top: 10px;
  }
  .select {
    padding: 5px;
    border-bottom: 1px solid #dadbde;
    &.no-input {
      color: #9095b2 !important;
    }
  }
}

</style>
