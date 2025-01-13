<template>
  <div class="main">
    <up-navbar title="出港申请" :autoBack="true"></up-navbar>
    <div class="content">
      <up-search shape="square" :showAction="false"></up-search>
      <up-cell-group>
        <up-cell title="船舶名称"> </up-cell>
        <up-cell title="船舶所有人"> </up-cell>
        <up-cell title="船舶所有人联系电话"> </up-cell>
        <up-cell title="出港渔港名称"> </up-cell>
        <up-cell title="计划出港时间"> </up-cell>
        <up-cell title="报备人"></up-cell>
        <up-cell title="报备手机号码"></up-cell>
      </up-cell-group>

    </div>
    <div class="title">船员管理</div>


    <div class="btn-group">
      <div class="btn" @click="saveLocal">暂存草稿</div>
      <div class="btn" @click="debounce">提交申请</div>
    </div>
  </div>
</template>
<script setup lang="ts">
onLoad((val: any) => {});
onMounted(() => {});
const formUser = ref();
const formUserData = ref({
  name: "",
  phone: "",
  idCard: "",
});
const rulesUser = reactive({
  name: [
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
  phone: [
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
  idCard: [
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
});
function saveLocal() {}
function debounce() {}
</script>
<style scoped lang="scss">
.main {
  background: $uni-bg-color-grey;
  .content {
    padding: 16px;
    box-sizing: border-box;
    height: auto;
  }
  .btn-group {
    position: absolute;
    bottom: 0;
    display: grid;
    width: 100%;
    height: 80px;
    grid-template-columns: repeat(2, 1fr);
    align-items: center;
    grid-gap: 10px;
    padding: 0 10px 0 10px;
    box-sizing: border-box;
    box-shadow: 0px -2px 1px rgba(0, 0, 0, 0.07);
    background: white;
    .btn {
      width: 100%;
      height: 40px;
      border-radius: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
    }

    .btn:nth-child(1) {
      background: #a8a8a8;
    }

    .btn:nth-child(2) {
      background: $uni-color-primary;
    }
  }
}
</style>
