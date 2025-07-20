<template>
  <div class="pcInfo-page">
    <!-- 左侧列表卡片区域 -->
    <div class="pcInfo-card">
      <div class="card-header">
        电脑信息管理
        <div class="actions">
          <el-button type="primary" size="small" @click="addVisible = true">添加</el-button>
        </div>
      </div>
      <div class="card-body">
        <div class="pcInfo-list">
          <div class="pcInfo-item" v-for="(item, index) in pageData" :key="index">
            <div class="pcInfo-title">{{ item.empName || "未知使用者" }}</div>
            <div class="pcInfo-content">IP 地址：{{ item.ip }}</div>
            <div class="pcInfo-content">
              内存总量：{{ item.memoryTotal }} GB
            </div>
            <div class="pcInfo-content">
              内存使用率：{{ item.memoryUsage }}%
            </div>
            <div class="pcInfo-content">
              磁盘信息：
              <div v-for="(disk, i) in parseDiskList(item.diskList as string)" :key="i">
                <span>{{ disk.mount }} {{ disk.used }}/{{ disk.total }}GB ({{
                  disk.usage
                }}%)</span>
              </div>
            </div>

            <div class="pcInfo-content">
              更新时间：{{ item.updateTime }}
            </div>

            <div class="pcInfo-footer">
              <el-button text size="small" type="primary" @click="handleEdit(item)">编辑</el-button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 右侧表格区域 -->
    <div class="pcInfo-table-card">
      <div class="card-header">详细信息</div>
      <div class="card-body">
        <el-table :data="pageData" border style="width: 100%">
          <el-table-column prop="empName" label="使用者" width="100" />
          <el-table-column prop="ip" label="IP 地址" width="140" />
          <el-table-column prop="memoryTotal" label="总内存 (GB)" width="100" />
          <el-table-column prop="memoryUsage" label="内存使用率 (%)" width="120" />
          <el-table-column label="磁盘信息" width="220" show-overflow-tooltip>
            <template #default="{ row }">
              <div v-if="row.diskList">
                <div v-for="(disk, i) in parseDiskList(row.diskList)" :key="i">
                  <span>{{ disk.mount }} {{ disk.used }}/{{ disk.total }}GB ({{
                    disk.usage
                  }}%)</span>
                </div>
              </div>
              <div v-else>无</div>
            </template>
          </el-table-column>
          <el-table-column prop="updateTime" label="更新时间" width="180" sortable />
          <el-table-column prop="createTime" label="创建时间" width="180" sortable/>

          <el-table-column label="操作" width="100" fixed="right">
            <template #default="{ row }">
              <el-button type="text" size="small" @click="handleEdit(row)">编辑</el-button>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页 -->
        <el-pagination class="t-pagination" background layout="prev, pager, next, sizes, total"
          :total="pcInfoStore.pageDataSum" :page-size="pcInfoStore.pageSize" :current-page="pcInfoStore.currentPage"
          @current-change="onCurrentChange" @size-change="onPageSizeChange" :page-sizes="[5, 10, 20, 50]" />
      </div>
    </div>

    <!-- 弹窗组件 -->
    <addPcInfo :visible="addVisible" @update:visible="handleAddVisibleChange" />
    <editPcInfo :visible="editVisible" @update:visible="handleEditVisibleChange" />
  </div>
</template>

<script setup lang="ts">
import {
  usePcInfoStore,
  pcInfoEditFormData,
  pageData,
} from "@/stores/pcInfo-store";

import { ref, onMounted } from "vue";
import type { PcInfoData } from "@/types/pcInfoTypes";

const pcInfoStore = usePcInfoStore();
const addVisible = ref(false);
const editVisible = ref(false);

// 编辑操作
const handleEdit = (item: PcInfoData) => {
  pcInfoEditFormData.value = { ...item }; // 填充编辑表单数据
  editVisible.value = true;
};

const handleAddVisibleChange = () => {
  addVisible.value = false;
};

const handleEditVisibleChange = () => {
  editVisible.value = false;
};

const onPageSizeChange = (size: number) => {
  pcInfoStore.pageSize = size;
  pcInfoStore.fetchPageData();
};

const onCurrentChange = (page: number) => {
  pcInfoStore.currentPage = page;
  pcInfoStore.fetchPageData();
};
const parseDiskList = (diskList: string | any[]): any[] => {
  if (Array.isArray(diskList)) return diskList;
  if (typeof diskList === "string") {
    try {
      return JSON.parse(diskList);
    } catch (e) {
      console.warn("磁盘信息解析失败", e);
      return [];
    }
  }
  return [];
};
onMounted(() => {
  pcInfoStore.fetchPageData();
});
</script>

<style lang="scss" scoped>
.pcInfo-page {
  display: flex;
  gap: 24px;
  height: 100%;
  box-sizing: border-box;
}

.pcInfo-card,
.pcInfo-table-card {
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  flex: 1;
}

.pcInfo-card {
  max-width: 420px;
  min-width: 320px;
}

.card-header {
  background: linear-gradient(90deg, #aee2ff, #cdeaff);
  padding: 16px 20px;
  font-weight: 600;
  font-size: 17px;
  color: #007acc;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eaeaea;
}

.card-body {
  padding: 20px;
  flex: 1;
  overflow: auto;
}

.actions {
  display: flex;
  align-items: center;
  gap: 10px;
}

.el-button {
  border-radius: 6px;
  transition: all 0.2s;
}

.el-button:hover {
  background-color: #ecf5ff;
  color: #409eff;
}

.pcInfo-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.pcInfo-item {
  padding: 14px 16px;
  background-color: #f6fbff;
  border-radius: 10px;
  border-left: 4px solid #409eff;
  box-shadow: 0 2px 8px rgba(0, 145, 255, 0.06);
  display: flex;
  flex-direction: column;
  gap: 6px;
  transition: all 0.2s;
}

.pcInfo-item:hover {
  transform: scale(1.01);
  box-shadow: 0 4px 16px rgba(0, 145, 255, 0.08);
}

.pcInfo-title {
  font-weight: bold;
  font-size: 16px;
  color: #007acc;
}

.pcInfo-content {
  font-size: 14px;
  color: #333;
  line-height: 1.4;
}

.pcInfo-footer {
  margin-top: 6px;
  text-align: right;
}

.pcInfo-table-card {
  flex: 2;
  min-width: 0;
}

.el-table {
  font-size: 14px;
  --el-table-border-color: #ebeef5;
}

.el-table-column {
  padding: 8px 0;
}

.t-pagination {
  margin-top: 20px;
  text-align: right;
}
</style>
