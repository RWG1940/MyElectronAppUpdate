<template>
  <div class="qw-card">
    <div class="section">
      <div class="label">一键同步企微用户与部门架构到本地数据库</div>
      <t-button theme="primary" @click="syncBtn">启动</t-button>
    </div>
    <div class="section">
      <div class="label">一键完成企微用户账号注册</div>
      <t-button theme="primary" @click="regBtn">启动</t-button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, onMounted } from "vue";
import { DialogPlugin, MessagePlugin } from "tdesign-vue-next";
import { regData,syncData } from "@/api/services/qywx-api";

const syncBtn = async () => {
  try {
    await syncData().then((res:any) => {
      if (res.data.code == "1") {
        MessagePlugin.success("同步成功");
      } else {
        MessagePlugin.error("同步失败：" + res.message);
      }
    })
  } catch (error) {
    MessagePlugin.error("同步过程中发生错误：" + error);
  }
};
const regBtn = async () => {
  try {
    await regData().then((res:any) => {
      if (res.data.code == "1") {
        MessagePlugin.success("注册成功");
      } else {
        MessagePlugin.error("注册失败：" + res.message);
      }
    })
  } catch (error) {
    MessagePlugin.error("注册过程中发生错误：" + error);
  }
};
</script>
<style lang="scss" scoped>
.qw-card {
  padding: 24px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.section {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 8px;
  border-bottom: 1px solid #f0f0f0;
}

.label {
  font-size: 14px;
  color: #333;
  font-weight: 500;
}

</style>
