<template>
  <div class="glass-wrap">
    <transition name="fade-scale" appear>
      <div class="glass-card">
        <div class="glass-header">
          <img src="../assets/img/seller-logo.png" class="logo" alt="logo" />
          <span>赛尔技术服务助手后台</span>
        </div>

        <t-form
          ref="form"
          :data="store.loginFormData"
          :rules="store.LOGIN_FORM_RULES"
          :label-width="0"
          @submit="store.loginOnSubmit"
          v-show="rwg"
        >
          <t-form-item name="account">
            <t-input
              v-model="store.loginFormData.account"
              clearable
              placeholder="账号"
              class="glass-input"
            >
              <template #prefix-icon><user-icon /></template>
            </t-input>
          </t-form-item>

          <t-form-item name="password">
            <t-input
              v-model="store.loginFormData.password"
              type="password"
              clearable
              placeholder="密码"
              class="glass-input"
            >
              <template #prefix-icon><lock-on-icon /></template>
            </t-input>
          </t-form-item>
          <!-- 
          <t-form-item name="验证码">
            <t-input
              v-model="store.sidentifyMode"
              placeholder="请输入验证码"
              @click="identifyCodeVisible = true"
              clearable
              class="glass-input"
            />
          </t-form-item> -->

          <!-- <div v-if="identifyCodeVisible" class="code-section">
            <t-row>
              <t-col :span="4">验证码：</t-col>
              <t-col :span="8">
                <SIdentify :identifyCode="store.identifyCode" />
              </t-col>
            </t-row>
          </div> -->

          <t-checkbox
            v-model="store.loginFormData.rememberMe"
            class="remember-me"
          >
            7天内记住密码
          </t-checkbox>

          <t-form-item>
            <t-button theme="primary" type="submit" block size="large"
              >登录</t-button
            >
          </t-form-item>
          <t-button
            style="margin-top: 10px"
            theme="success"
            @click="qywxLoginVisible = true; rwg = false;"
            block
            size="large"
            >企业微信扫码登录</t-button
          >
        </t-form>
        <transition name="fade-scale" appear>
          <div class="qywx-login" v-show="qywxLoginVisible">
            <div id="qywx-login-container"></div>
          </div>
        </transition>
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useUserStore } from "@/stores/user-store";
import { LockOnIcon, UserIcon } from "tdesign-icons-vue-next";
import { useRouter, useRoute } from 'vue-router'
import { MessagePlugin } from "tdesign-vue-next";

const rwg = ref(true);
const qywxLoginVisible = ref(false);
const store = useUserStore();
// 企业微信扫码登录
function initQywxQrcode() {

  const container = document.getElementById("qywx-login-container");
  if (container) {
    container.innerHTML = "";
    const appid = "wwbf157b81fe36259a";
    const agentid = "1000179";
    const redirect_uri = encodeURIComponent(
      "http://cloud.sellersuniononline.com:18086"
    );
    const state = "fuwu";
    const iframe = document.createElement("iframe");
    iframe.src = `https://open.work.weixin.qq.com/wwopen/sso/qrConnect?appid=${appid}&agentid=${agentid}&redirect_uri=${redirect_uri}&state=${state}&login_type=jssdk&version=1.2.7`;
    iframe.style.width = "200px";
    iframe.style.height = "300px";
    iframe.style.border = "none";
    container.appendChild(iframe);
  }
}


onMounted(() => {
  if (localStorage.getItem("token")) {
    store.autoLogin();
  }
  const route = useRoute();
  const token = route.query.token as string;
  if (token) {
    localStorage.setItem("token", token);
    store.token = token;
    store.autoLogin();
  } 
  initQywxQrcode();
});
</script>
<style scoped>
.glass-wrap {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  /* background: linear-gradient(135deg, #c3ecf7, #a7bfe8, #fbc2eb); */
  background-size: 100%;
  background-image: url("../assets/img/loginPage/background.jpg");
  animation: gradient-move 425s ease infinite;
}

@keyframes gradient-move {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

.glass-card {
  backdrop-filter: blur(30px);
  background-color: rgba(255, 255, 255, 0.15);
  border: 1px solid rgba(255, 255, 255, 0.3);
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.3);
  border-radius: 20px;
  padding: 40px;
  width: 380px;
  animation: pop-in 1s ease;
}

@keyframes pop-in {
  from {
    transform: scale(0.8);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

.glass-header {
  text-align: center;
  margin-bottom: 30px;
}

.glass-header .logo {
  width: 30px;
  height: 30px;
  margin-right: 10px;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.2));
}

.glass-header span {
  font-size: 24px;
  font-weight: bold;
  color: #ffffffdd;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

.glass-input :deep(.t-input__wrap) {
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 12px;
  transition: 0.3s;
}

.glass-input :deep(.t-input__wrap):focus-within {
  background-color: rgba(255, 255, 255, 0.3);
  box-shadow: 0 0 0 2px rgba(255, 255, 255, 0.3);
}

.code-section {
  margin: 10px 0;
}

.remember-me {
  margin: 15px 0;
  color: white;
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: scale(0.9);
}

.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.5s ease;
}
.qywx-login {
  text-align: center;
}

</style>
