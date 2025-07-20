<template>
  <div class="wrap">
    <div class="search-panel">
      <t-row :gutter="16" align="middle" justify="start">
        <t-col :span="2">
          <t-input v-model="store.searchData.id" placeholder="部门 ID" clearable />
        </t-col>
        <t-col :span="2">
          <t-input v-model="store.searchData.dName" placeholder="部门名称" clearable />
        </t-col>
        <t-col >
          <t-date-range-picker
            :value="[store.createTime1, store.createTime2]"
            placeholder="创建时间范围"
            @pick="onPick"
            @change="onChange"
            enable-time-picker
            allow-input
          />
        </t-col>
        <t-col >
          <t-button theme="primary" @click="store.searchDept()">
            <template #icon><search-icon /></template>搜索
          </t-button>
        </t-col>
        <t-col >
          <t-button theme="default" variant="outline" @click="store.searchDeptDataRefresh()">
            <template #icon><refresh-icon /></template>重置
          </t-button>
        </t-col>
        <t-col >
          <t-tooltip content="新增部门">
            <t-button @click="addButton()" theme="default" shape="circle" size="medium">
              <template #icon><add-icon /></template>
            </t-button>
          </t-tooltip>
        </t-col>
        <t-col >
          <t-tooltip content="批量删除">
            <t-button @click="store.handleBatchDelete()" theme="danger" shape="circle" size="medium">
              <template #icon><delete-icon /></template>
            </t-button>
          </t-tooltip>
        </t-col>
      </t-row>
    </div>

    <div class="table-section">
      <deptTable v-model:addVisible="addVisible" @update:addVisible="handleUpdateAddVisible" />
      <pagination class="pag" />
    </div>
  </div>
</template>

<script setup lang="ts">
import deptTable from './deptManage/deptTable.vue'
import { ref, onMounted } from "vue";
import pagination from "@/components/pagination.vue";
import { useDeptStore } from "@/stores/dept-store";
import type { DateRangePickerProps } from 'tdesign-vue-next';

const store = useDeptStore()

// 组件挂载后加载部门数据ByToken
onMounted(() => {
  store.handlePageChange()
});
const onPick: DateRangePickerProps['onPick'] = (value: any, context) => {
  store.createTime1 = value[0]
  store.createTime2 = value[1]
}
const onChange: DateRangePickerProps['onChange'] = (value: any, context) => {
  store.createTime1 = value[0]
  store.createTime2 = value[1]
};
//新增用户面板的可视化以及回调
const addVisible = ref(false)
const addButton = () => {
  addVisible.value = true
}
const handleUpdateAddVisible = () => {
  addVisible.value = false;
};

</script>
<style lang="scss" scoped>
.wrap {
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  padding: 20px;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.search-panel {
  padding: 12px 0;
  border-bottom: 1px solid #f0f0f0;
  margin-bottom: 16px;
}

.table-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: stretch;
  justify-content: flex-start;
}

.pag {
  margin-top: 16px;
  align-self: flex-end;
}
</style>
  