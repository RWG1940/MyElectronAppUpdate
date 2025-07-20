<template>
  <div class="rwg1">
    <tags />
    <t-list class="rwg2">
      <transition name="m-trans" appear>
        <router-view></router-view>
      </transition>
      <t-back-top container=".rwg" :visible-height="200" style="position: absolute" :offset="['24px', '80px']"
        :duration="500" shape="circle"></t-back-top>
        <my-info />
    </t-list>
    
  </div>
</template>
<script lang="ts" setup>
import myInfo from "@/layout/components/myInfo.vue";
import { ref } from "vue";
import tags from "@/layout/components/tags.vue";



</script>
<style scoped>
.m-trans-enter-active,
.m-trans-leave-active {
  transition: opacity 0.4s ease, transform 0.4s ease;  /* 分开动画效果以提高性能 */
  opacity: 1;
}

.m-trans-enter-from,
.m-trans-leave-to {
  opacity: 0;
  transform: perspective(1000px) translateX(-150px);  /* 调整起始位置 */
}

.m-trans-enter-to,
.m-trans-leave-from {
  opacity: 1;
  transform: perspective(1000px) translateX(0);  /* 目标位置 */
}

.rwg1 {
  background-color: rgba(146, 146, 146, 0.16);
}

.rwg2 {
  background-color: rgb(255, 255, 255, 0);
  border-radius: 5px;
  overflow-y: scroll;
  padding: 0 1%;
  height: 80vh; /* 默认高度 */
}

@media (min-width: 1025px) {
  .rwg2 {
    height: 80vh;
  }
}
@media (min-width: 1500px) {
  .rwg2 {
    height: 83vh;
  }
}

</style>