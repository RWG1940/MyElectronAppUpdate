<template>
  <div class="wrap">
    <t-input
      v-model="searchKeyword"
      placeholder="请输入关键词搜索设置项"
      class="search-bar"
      clearable
    >
      <template #prefix-icon>
        <search-icon />
      </template>
    </t-input>

    <div
      class="section"
      v-for="block in filteredBlocks"
      :key="block.title"
    >
      <div class="section-title">{{ block.title }}</div>
      <div class="section-body">
        <component :is="block.component" />
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, computed } from "vue";
import { SearchIcon } from 'tdesign-icons-vue-next';

import routesTable from "./settingsPage/routesTable.vue";
import rolesTable from "./settingsPage/rolesTable.vue";
import roleRoutesTable from "./settingsPage/roleRoutesTable.vue";
import permissionsTable from "./settingsPage/permissionsTable.vue";
import EmpPermissionsTable from "./settingsPage/empPermissionsTable.vue";
import qySync from "./settingsPage/qySync.vue";

const blocks = [
  { title: "路由设置", component: routesTable },
  { title: "角色设置", component: rolesTable },
  { title: "角色-路由设置", component: roleRoutesTable },
  { title: "权限设置", component: permissionsTable },
  { title: "用户-权限设置", component: EmpPermissionsTable },
  { title: "企微同步", component: qySync }
];

// 搜索关键词
const searchKeyword = ref("");

// 过滤后的模块列表
const filteredBlocks = computed(() =>
  blocks.filter((block) =>
    block.title.includes(searchKeyword.value.trim())
  )
);
</script>
<style lang="scss" scoped>
.wrap {
 
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.section {
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
  padding: 20px;
  transition: all 0.2s ease;
  border-left: 4px solid #2d8cf0;
}

.section:hover {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.section-title {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
  color: #2d8cf0;
  border-bottom: 1px solid #eee;
  padding-bottom: 4px;
}

.search-bar {
  width: 320px;
}

</style>
