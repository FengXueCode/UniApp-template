<template>
  <div class="main">
    <div class="content">
      <up-navbar title="船员管理" :autoBack="true"></up-navbar>

      <up-search
        shape="square"
        :showAction="false"
        placeholder="请输入"
      ></up-search>

      <div class="list">
        <up-list @scrolltolower="scrolltolower">
          <up-list-item v-for="(item, index) in list" :key="index">
            <div class="item" @click="item.checked = !item.checked;sumNumber()">
              <div class="check">
                <up-checkbox
                  v-model:checked="item.checked"
                  usedAlone
                  @change="changeChecked"
                ></up-checkbox>
              </div>

              <div class="item-info">
                <div class="head">
                  <div class="head-img-back">
                    <img
                      src="../static/icon/my/harbour-user.svg"
                      alt=""
                      class="icon"
                    />
                  </div>
                  <div class="head-title">{{ item.crewName }}</div>
                </div>

                <div class="info">
                  <div class="info-text">户籍：{{ item.crewRegister }}</div>
                  <div class="info-text">联系方式：{{ item.crewPhone }}</div>
                </div>

                <div class="info">
                  <div class="info-text">
                    现居住地址：{{ item.crewCurrentResidence }}
                  </div>
                </div>
              </div>
            </div>
          </up-list-item>
        </up-list>
      </div>
    </div>

    <div class="bottom-btn">
      <div class="check">
        <up-checkbox
          usedAlone
          shape="circle"
          label="全选"
          :checked="number == list.length"
          @change="checkAll"
        ></up-checkbox>
      </div>
      <div class="text">已选中：{{ number }}</div>
      <div class="btn check" @click="sure">确认</div>
    </div>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {
  checkedList.value = JSON.parse(val.list)
  
});
const checkedList = ref([])
onMounted(() => {
  getList();
});

function scrolltolower() {
  if (pageObj.value.pageNumber >= pageObj.value.total / 20) return;
  pageObj.value.pageNumber++;
  // getList(true);
}
const pageObj = ref({
  pageNumber: 1,
  pageSize: 20,
  total: 0,
});

//----------------------<xz-选择>----------------------
const list = ref([]);
function getList(isMore: boolean = false) {
  request("/fisherApi/wechatMiniProgram/findCrewByUserId", {}, "POST")
    .then((res) => {
      res.forEach(element=>{
        checkedList.value.forEach(item=>{
          if(element.cbCrewId==item.cbCrewId){
            element.checked = true
          }
        })
      })
      list.value = res;
    })
    .catch((err) => {
      console.log("err", err);
    });
}
const number = ref(0);

function changeChecked(e) {
  setTimeout(() => {
    sumNumber();
  }, 200);
}
function sumNumber() {
  number.value = 0;
  list.value.forEach((element: any) => {
    if (element.checked) {
      number.value++;
    }
  });
}
function checkAll(e: any) {
  list.value.forEach((element: any) => {
    element.checked = e;
  });
  sumNumber();
}
function sure() {
  let arr = []
  list.value.forEach(element=>{
    if(element.checked==true){
      arr.push(element)
    }
  })
  uni.$emit("selectHarbour",{arr:JSON.stringify(arr)})
  util.back()
}
</script>
<style scoped lang="scss">
.content {
  width: 100%;
  height: calc(100% - 80px);
  .list {
    width: 100%;
    height: calc(100% - 50px);
    padding: 0 16px;
    box-sizing: border-box;
    overflow-y: auto;
    margin-top: 10px;
    .item {
      padding: 10px 16px;
      box-sizing: border-box;
      border-bottom: 1px solid $uni-border-color;
      display: flex;
      align-items: center;
      .check {
      }
      .item-info {
        margin-left: 10px;
        .head {
          display: flex;
          align-items: center;
          margin-bottom: 10px;
          .head-img-back {
            background: #0086fdff;
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
          .head-title {
            margin-left: 30px;
            font-size: 17px;
          }
        }
        .info {
          display: flex;
          justify-content: space-between;
          align-items: center;
          color: #999999ff;
          font-size: 12px;
          margin-bottom: 5px;
        }
      }
    }
  }
}
.bottom-btn {
}
</style>
