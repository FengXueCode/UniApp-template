<template>
  <div class="main">
    <up-navbar :title="!inOut ? '进港申报' : '出港申报'" :autoBack="true"></up-navbar>
    <div class="content">
      <div class="ship-info">
        <up-form :model="formData" labelAlign="right" ref="form" :rules="rules">
          <div class="reject" v-if="formData.sailingHarbourAuditState == 2">
            驳回意见
            <div class="text">{{ formData.sailingHarbourAuditFeedback }}</div>
          </div>

          <div class="declare-state" v-if="isEdit">
            <div class="title">
              {{ formData.harbourName }}-{{ !inOut ? "进港申报" : "出港申报" }}
            </div>
            <div class="state" :class="{ reject: formData.sailingHarbourAuditState == 2 }">
              {{ initState(formData.sailingHarbourAuditState) }}
            </div>
          </div>

          <up-form-item label="船舶" prop="cbShipId" labelWidth="120">
            <div class="select" :class="{ 'no-input': formData.cbShipId == '' }" @click="showPicker(0)">
              <div class="text">
                {{ formData.cbShipId == "" ? "请选择船舶" : ship.shipName }}
              </div>

              <up-icon name="arrow-right" v-if="formData.sailingHarbourAuditState !=0&&formData.sailingHarbourAuditState!=90 "></up-icon>
            </div>

            <up-picker @cancel="showShip = false" confirmColor="#07C160" :show="showShip" :columns="[shipList]"
              :defaultIndex="[checkShip]" keyName="shipName" @confirm="sureShip"></up-picker>
          </up-form-item>
          <div class="user-info">
            <div class="item">
              <div class="label">船舶所有人</div>
              <div class="text">{{ initNull(ship.shipOwner) }}</div>
            </div>
            <div class="item">
              <div class="label">联系方式</div>
              <div class="text">{{ initNull(ship.shipOwnerContact) }}</div>
            </div>
          </div>

          <up-form-item :label="inOut ? '出港口' : '进港口'" prop="harbourNo" labelWidth="120">
            <div class="select" :class="{ 'no-input': formData.harbourNo == '' }" @click="showPicker(1)">
              <div class="text">
                {{ formData.harbourNo == "" ? "请选择港口" : harbour.desc }}
              </div>
              <up-icon name="arrow-right" v-if="formData.sailingHarbourAuditState !=0&&formData.sailingHarbourAuditState!=90 "></up-icon>
            </div>

            <up-picker @cancel="showHarbour = false" confirmColor="#07C160" :show="showHarbour" :columns="[harhourList]"
              :defaultIndex="[checkHarbour]" keyName="desc" @confirm="sureHarbour"></up-picker>
          </up-form-item>
          <up-form-item :label="inOut ? '计划出港时间' : '计划进港时间'" prop="plannedDate" labelWidth="120">
            <div class="select" :class="{ 'no-input': formData.plannedDate == '' }" @click="showPicker(2)">
              <div class="text">
                {{
                  formData.plannedDate == ""
                    ? "请选择计划时间"
                    : formData.plannedDate
                }}
              </div>
              <up-icon name="arrow-right" v-if="formData.sailingHarbourAuditState !=0&&formData.sailingHarbourAuditState!=90 "></up-icon>
            </div>
            <up-datetime-picker :show="showDate" :minDate="Date.now()" mode="datetime" v-model="formData.plannedDate"
              @cancel="showDate = false" @confirm="sureDate"></up-datetime-picker>
          </up-form-item>
          <up-form-item label="报备人" prop="proposerName" labelWidth="120">
            <up-input placeholder="请输入报备人" :disabled="formData.sailingHarbourAuditState != undefined &&
              formData.sailingHarbourAuditState != 2
              " type="text" maxlength="10" border="bottom" v-model="formData.proposerName"></up-input>
          </up-form-item>
          <up-form-item label="报备人联系方式" prop="proposerContact" labelWidth="120">
            <up-input placeholder="请输入联系方式" :disabled="formData.sailingHarbourAuditState != undefined &&
              formData.sailingHarbourAuditState != 2
              " type="text" maxlength="11" border="bottom" v-model="formData.proposerContact"></up-input>
          </up-form-item>
        </up-form>
      </div>

      <div class="user-list">
        <div class="title">
          <div class="text">船员管理</div>
          <div class="btn" @click="addHarbour" v-if="formData.sailingHarbourAuditState!=0&&formData.sailingHarbourAuditState!=90">
            <up-icon name="plus" color="white"></up-icon>
            <div class="text">新增船员</div>
          </div>
        </div>
        <div class="list">
          <div class="empty" v-if="cbCrews.length == 0">
            <up-empty mode="list" text="暂无船员"></up-empty>
          </div>
          <div class="item" v-for="(item, index) in cbCrews" :key="index">
            <div class="item-state" :class="{ 'is-owner': item.crewPhone === ship.shipOwnerContact }">
              {{
                item.crewPhone === ship.shipOwnerContact ? "船舶所有人" : "船员"
              }}
            </div>
            <div class="item-info">
              {{ item.crewName }}({{ item.crewPhone }})
            </div>
            <up-icon name="trash" color="#FF9696" size="20" @click="removeCrew(item)"></up-icon>
          </div>
        </div>
      </div>
    </div>

    <div class="bottom-btn">
      <div class="btn-group" v-if="!isEdit">
        <div class="local item" @click="saveLocal">暂存本地</div>
        <div class="submit item" @click="submit">提交申报</div>
      </div>
      <div class="btn-full" v-if="formData.sailingHarbourAuditState == 2" @click="submit">
        <div class="submit item">修改申请</div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {
  uni.$once("selectHarbour", function (data) {
    cbCrews.value = JSON.parse(data.arr);
  });
  if (val.inOut) {
    inOut.value = JSON.parse(val.inOut);
  }
  if (val.isEdit) {
    isEdit.value = true;
    formData.value = JSON.parse(val.record);
  }
  setTimeout(() => {
    if (val.isEdit) {
      initLocal();
      getMariner();
    } else {
      if (inOut.value && uni.getStorageSync("out") != "") {
        formData.value = JSON.parse(uni.getStorageSync("out"));
        initLocal();
      }
      if (!inOut.value && uni.getStorageSync("in") != "") {
        formData.value = JSON.parse(uni.getStorageSync("in"));
        initLocal();
      }
    }
  }, 600);
});

function initLocal() {
  cbCrews.value = formData.value.cbCrews == null ? [] : formData.value.cbCrews;
  ship.value = shipList.value.find(
    (element) => element.cbShipId == formData.value.cbShipId,
  );
  checkShip.value = shipList.value.findIndex(
    (element) => element.cbShipId == formData.value.cbShipId,
  );
  harbour.value = harhourList.value.find(
    (element) => element.value == formData.value.harbourNo,
  );
  checkHarbour.value = harhourList.value.findIndex(
    (element) => element.value == formData.value.harbourNo,
  );
}
onMounted(() => {
  getShipList();
  getHarbour();
});
const inOut = ref(false); // false 进 true 出
const isEdit = ref(false);
const user = uni.getStorageSync("user");
const form = ref();
const formData = ref({
  cbShipId: "",
  harbourNo: "",
  harbourName: "",
  plannedDate: "",
  proposerName: user.userName,
  proposerContact: "",
});
const cbCrews = ref([]);
const rules = reactive({
  cbShipId: [
    {
      type: "string",
      required: true,
      message: "请选择船舶",
      trigger: ["blur", "change"],
    },
  ],
  harbourNo: [
    {
      required: true,
      message: "请选择港口",
      trigger: ["blur", "change"],
    },
  ],
  plannedDate: [
    {
      required: true,
      message: "请选择计划时间",
      trigger: ["blur", "change"],
    },
  ],
  proposerName: [
    {
      required: true,
      message: "请输入报备人",
      trigger: ["blur", "change"],
    },
  ],
  proposerContact: [
    {
      required: true,
      message: "请输入报备人联系方式",
      trigger: ["blur", "change"],
    },
  ],
  cbCrews: [
    {
      validator: (rule, value, callback) => {
        return value.length != 0;
      },
      message: "请选择船员",
    },
  ],
});
function addHarbour() {
  if (
    formData.value.sailingHarbourAuditState != undefined &&
    formData.value.sailingHarbourAuditState != 2
  )
    return;

  util.go("/pageIndex/selectHarbour?list=" + JSON.stringify(cbCrews.value));
}
const initNull = util.initNull;
function initState(state: number) {
  switch (state) {
    case 0:
      return "待审核";
    case 1:
      return "审核中";
    case 2:
      return "驳回";
    case 3:
      return "自动作废";
    case 4:
      return "缺失纠正";
    case 5:
      return "违规记录";
    case 90:
      return "审核通过";
  }
}
//----------------------<kztc-控制弹窗>----------------------
// 0 船舶 1 港口 2 时间
function showPicker(type: number) {
  if (
    formData.value.sailingHarbourAuditState != undefined &&
    formData.value.sailingHarbourAuditState != 2
  )
    return;

  switch (type) {
    case 0:
      showShip.value = true;
      break;
    case 1:
      showHarbour.value = true;
      break;
    case 2:
      showDate.value = true;
      break;
  }
}
//----------------------<hqcy-获取船员>----------------------
function getMariner() {
  request(
    "/fisherApi/wechatMiniProgram/findCrewByShip",
    { cbShipId: ship.value.cbShipId },
    "POST",
  )
    .then((res) => {
      cbCrews.value = res;
    })
    .catch((err) => {
      console.log("err", err);
    });
}
//----------------------<sccy-删除船员>----------------------
function removeCrew(item: any) {
  if (
    formData.value.sailingHarbourAuditState != undefined &&
    formData.value.sailingHarbourAuditState != 2
  )
    return;
  uni.showModal({
    title: "确认删除?",
    success(res) {
      if (res.confirm) {
        cbCrews.value = cbCrews.value.filter(
          (element) => element.cbCrewId != item.cbCrewId,
        );
      }
    },
  });
}
//----------------------<tjsb-提交申报>----------------------
function submit() {
  uni.showModal({
    title: "确认提交？",
    success(res) {
      if (res.confirm) {
        if (cbCrews.value.length == 0) {
          util.toast("请选择船员");
          return;
        }
        formData.value.sailingHarbourApplyType = inOut.value ? 0 : 1;
        formData.value.sailingHarbourPersonNum = cbCrews.value.length;
        form.value.validate().then((valid) => {
          if (valid) {
            request(
              isEdit.value
                ? "/fisherApi/wechatMiniProgram/editShipEnterOuterHarbour"
                : "/fisherApi/wechatMiniProgram/saveShipEnterOuterHarbour",
              {
                cbShipEnterOuterHarbour: formData.value,
                cbCrews: cbCrews.value,
              },
              "POST",
            )
              .then((res) => {
                uni.removeStorageSync(inOut.value ? "out" : "in");
                util.toast("提交成功");
                util.back();
              })
              .catch((err) => { });
          }
        });
      }
    },
  });
}
//----------------------<zcbd-暂存本地>----------------------
function saveLocal() {
  uni.showModal({
    title: "确认暂存？",
    success(res) {
      if (res.confirm) {
        uni.setStorageSync(
          inOut.value ? "out" : "in",
          JSON.stringify(formData.value),
        );
        util.back();
      }
    },
  });
}
//----------------------<jhsj-计划时间>----------------------
const plannedDate = ref("");
const showDate = ref(false);
function sureDate(e) {
  // plannedDate.value = util.formaDate(e.value);
  formData.value.plannedDate = util.formaDate(e.value);
  showDate.value = false;
}
//----------------------<cb-船舶>----------------------
const showShip = ref(false);
const shipList = ref([]);
const ship = ref({ shipName: "", shipOwner: "", shipOwnerContact: "" });
function getShipList() {
  request("/fisherApi/wechatMiniProgram/findShipByUserId", {}, "POST")
    .then((res) => {
      shipList.value = res;
    })
    .catch((err) => { });
}
const checkShip = ref(0);
function sureShip(e) {
  checkShip.value = e.indexs[0];
  showShip.value = false;
  ship.value = e.value[0];
  formData.value.cbShipId = e.value[0].cbShipId;
  formData.value.shipName = e.value[0].shipName;
}
//----------------------<cjg-船籍港>----------------------
const harhourList = ref([]);
const harbour = ref({ desc: "", value: "" });
const showHarbour = ref(false);
const checkHarbour = ref(0);

function getHarbour() {
  request("/fisherApi/wechatMiniProgram/pullDownValueHarbourNo", {}, "POST")
    .then((res) => {
      harhourList.value = res;
    })
    .catch((err) => { });
}
function sureHarbour(e) {
  checkHarbour.value = e.indexs[0];
  showHarbour.value = false;
  harbour.value = e.value[0];
  formData.value.harbourNo = harbour.value.value;
  formData.value.harbourName = harbour.value.desc;
}
</script>
<style scoped lang="scss">
.main {
  background: $uni-bg-color-grey;

  .content {
    width: 100%;
    height: calc(100vh - 80px - 20px);
    overflow-y: auto;
  }

  .ship-info {
    background: white;
    margin-top: 10px;
    box-sizing: border-box;

    .reject {
      margin-bottom: 10px;

      .text {
        margin-top: 10px;
        padding: 10px;
        background: #f7f7f7;
        color: #f92800;
        box-sizing: border-box;
      }
    }

    .declare-state {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 40px;
      font-size: 16px;

      .title {
        font-weight: bolder;
      }

      .state {
        color: white;
        position: relative;
        height: 30px;
        width: 100px;
        background: #0086fd;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-right: -16px;
        padding-left: 10px;

        &.reject {
          background: red;
        }
      }

      .state::before {
        content: "";
        position: absolute;
        left: -20px;
        top: 0px;
        width: 30px;
        height: 30px;
        background-color: white;
        transform: rotate(45deg);
        transform-origin: center;
      }
    }

    .user-info {
      background: #f7f7f7;
      padding: 10px;

      .item {
        display: flex;

        .label {
          width: 120px;
          text-align: end;
          box-sizing: border-box;
          padding-right: 15px;
          font-size: 15px;
          color: #303133;
          box-sizing: border-box;
        }

        .text {
          margin-left: 0px;
          color: #333333;
        }
      }

      .item:nth-child(1) {
        margin-bottom: 10px;
      }
    }
  }

  .user-list {
    background: white;
    margin-top: 10px;

    .title {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 10px;
      box-sizing: border-box;
      border-bottom: 1px solid $uni-border-color;
      height: 44px;

      .text {}

      .btn {
        display: flex;
        align-items: center;
        justify-content: center;
        background: #0086fd;
        color: white;
        width: 108px;
        height: 28px;
        border-radius: 30px;
        font-size: 14px;

        .text {
          margin-left: 10px;
        }
      }
    }

    .list {
      height: calc(100% - 44px);
      box-sizing: border-box;
      padding: 0 10px;
      overflow-y: auto;

      .item {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 10px 10px;
        border-bottom: 1px solid $uni-border-color;

        .item-state {
          padding: 5px 10px;
          color: white;
          height: 22;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 12px;
          background: $uni-color-primary;

          &.is-owner {
            background: $uni-color-zt-2;
          }
        }

        .item-info {
          flex-grow: 1;
          margin-left: 10px;
        }
      }
    }
  }
}
</style>
