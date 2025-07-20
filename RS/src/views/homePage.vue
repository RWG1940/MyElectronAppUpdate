<template>
  <div class="home-layout">
    <div class="header">
      <rheader />
    </div>
    <div class="main-area">
      <div class="sidebar">
        <sideBar />
      </div>
      <div class="content-area">
        <div class="content">
          <content />
        </div>
        <div class="footer">
          <rfooter />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import sideBar from "@/layout/sideBar.vue";
import rheader from "@/layout/header.vue";
import content from "@/layout/content.vue";
import rfooter from "@/layout/footer.vue";

</script>
<style scoped>
.home-layout {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
.header {
  flex: 0 0 auto;
}
.main-area {
  display: flex;
  flex: 1 1 0;
  min-height: 0;
}
.sidebar {
  flex-shrink: 0;
  background: #f5f5f5;
  min-height: 0;
  overflow: auto;
}
.content-area {
  display: flex;
  flex-direction: column;
  flex: 1 1 0;
  min-width: 0;
  min-height: 0;
}
.content {
  overflow: auto;
}
.footer {
  flex: 0 0 auto;
}
</style>