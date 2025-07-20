<template>
  <div class="wrap">
    <!-- 筛选区域 -->
    <div class="filter-panel">
      <t-row :gutter="16" align="middle" justify="start" class="form-row">
        <t-col :span="2">
          <t-input v-model="store.searchUserData.emp.id" placeholder="ID" clearable />
        </t-col>
        <t-col :span="2">
          <t-input v-model="store.searchUserData.emp.eUsername" placeholder="账号" clearable />
        </t-col>
        <t-col :span="2">
          <t-input v-model="store.searchUserData.emp.eName" placeholder="姓名" clearable />
        </t-col>
        <t-col :span="2">
          <t-input v-model="store.searchUserData.emp.ePhone" placeholder="手机号" clearable />
        </t-col>
        <t-col :span="2">
          <t-input v-model="store.searchUserData.emp.eId" placeholder="证件号" clearable />
        </t-col>
        <t-col :span="2">
          <t-select v-model="store.searchUserData.emp.eDeptid" placeholder="部门" clearable>
            <t-option v-for="item in storeDept.tableData" :key="item.id" :value="item.id" :label="item.dName" />
          </t-select>
        </t-col>
        <t-col :span="2">
          <t-select v-model="store.searchUserData.role.rId" placeholder="角色" clearable>
            <t-option v-for="item in storeRole.roleList" :key="item.rId" :value="item.rId" :label="item.rInfo" />
          </t-select>
        </t-col>
        <t-col :span="2">
          <t-select v-model="store.searchUserData.emp.eIsenabled" placeholder="状态" clearable>
            <t-option value="1" label="启用" />
            <t-option value="0" label="禁用" />
          </t-select>
        </t-col>
        <t-col :span="2">
          <t-select v-model="store.searchUserData.emp.eGender" placeholder="性别" clearable>
            <t-option value="1" label="男" />
            <t-option value="0" label="女" />
          </t-select>
        </t-col>
        <t-col :span="4">
          <t-date-range-picker
            :value="[store.createTime1, store.createTime2]"
            placeholder="入职日期范围"
            enable-time-picker
            allow-input
            clearable
            @pick="onPick"
            @change="onChange"
          />
        </t-col>
      </t-row>

      <!-- 操作按钮区 -->
      <t-row :gutter="12" class="action-row" justify="start">
        <t-col >
          <t-button theme="primary" @click="store.searchUser">
            <template #icon><search-icon /></template>搜索
          </t-button>
        </t-col>
        <t-col >
          <t-button variant="outline" @click="store.searchUserDataRefresh">
            <template #icon><refresh-icon /></template>重置
          </t-button>
        </t-col>
        <t-col >
          <t-tooltip content="新增用户">
            <t-button shape="circle" theme="default" @click="addButton">
              <template #icon><add-icon /></template>
            </t-button>
          </t-tooltip>
        </t-col>
        <t-col >
          <t-tooltip content="批量删除">
            <t-button shape="circle" theme="danger" @click="store.handleBatchDelete">
              <template #icon><delete-icon /></template>
            </t-button>
          </t-tooltip>
        </t-col>
      </t-row>
    </div>

    <!-- 表格 & 分页 -->
    <div class="table">
      <userTable v-model:addVisible="addVisible" @update:addVisible="handleUpdateAddVisible" />
      <pagination class="pag" />
    </div>
  </div>
</template>

<script setup lang="ts">
import userTable from './userManage/userTable.vue'
import { ref, onMounted } from "vue";
import pagination from "@/components/pagination.vue";
import { useUserStore } from "@/stores/user-store";
import { useDeptStore } from "@/stores/dept-store";
import { useRoleStore } from "@/stores/role-store";
import type { DateRangePickerProps } from 'tdesign-vue-next';

const store = useUserStore()
const storeDept = useDeptStore()
const storeRole = useRoleStore()

const onPick: DateRangePickerProps['onPick'] = (value: any, context) => {
  store.createTime1 = value[0]
  store.createTime2 = value[1]
}
const onChange: DateRangePickerProps['onChange'] = (value: any, context) => {
  store.createTime1 = value[0]
  store.createTime2 = value[1]
};
onMounted(() => {
  store.handlePageChange()
});

const addVisible = ref(false)
const addButton = () => {
  store.cleanUserData();
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
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
  padding: 20px;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.filter-panel {
  margin-bottom: 16px;
}

.form-row + .action-row {
  margin-top: 12px;
}

.t-col {
  margin-bottom: 10px;
}

.table {
  display: flex;
  flex-direction: column;
  align-items: stretch;
}

.pag {
  margin-top: 20px;
  align-self: flex-end;
}
</style>

