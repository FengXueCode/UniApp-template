<template>
  <div class="main">
    <up-navbar :autoBack="true" title="个人信息完善"></up-navbar>
    <div class="user-info">
      <up-form :model="formUserData" ref="formUser" :rules="rulesUser" labelAlign="right">
        <up-form-item label="姓名" prop="name" labelWidth="80">
          <up-input
            placeholder="请输入姓名"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="formUserData.name"
          ></up-input>
        </up-form-item>

        <up-form-item label="联系方式" prop="phone" labelWidth="80">
          <up-input
            placeholder="请输入联系方式"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="formUserData.phone"
          ></up-input>
        </up-form-item>
        <up-form-item label="身份证号" prop="idCard" labelWidth="80">
          <up-input
            placeholder="请输入身份证号"
            type="text"
            maxlength="10"
            border="bottom"
            v-model="formUserData.idCard"
          ></up-input>
        </up-form-item>
      </up-form>
    </div>
    <div class="bottom-btn">
      <div class="btn">保存</div>
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

</script>
<style scoped lang="scss">
.main {
  background: #FAFCFFFC;
  .user-info{
    padding: 16px;
    box-sizing: border-box;
    margin-top: 10px;
    background: white;
  }
}
</style>
