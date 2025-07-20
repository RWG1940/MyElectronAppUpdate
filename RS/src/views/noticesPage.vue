<template>
    <div class="notices-page">
        <!-- 左侧：已发布公告 -->
        <div class="notice-card">
            <div class="card-header">
                <span><t-icon name="notification" /> 已发布公告</span>
                <div class="actions">
                    <t-tooltip content="新增一条公告">
                        <t-button size="small" @click="handleAddVisibleChange" ><t-icon
                                name="add"></t-icon></t-button>
                    </t-tooltip>
                    <t-tooltip content="删除过期的公告">
                        <t-popconfirm theme="default" content="您确定要删除24小时之前的公告吗" :visible="visible1" :confirm-btn="{
                            content: '确认',
                            theme: 'warning',
                            onClick: () => {
                                deleteExpiredNoticesData()
                                visible1 = false
                            }
                        }" :cancel-btn="{
                            content: '我再想想',
                            theme: 'default',
                            variant: 'outline',
                            onClick: () => { visible1 = false }
                        }">
                            <t-button size="small" style="margin-left: 5px;" @click="visible1 = true"
                                ><t-icon name="delete-time"></t-icon></t-button>
                        </t-popconfirm>
                    </t-tooltip>
                </div>
            </div>
            <div class="card-body">
                <div class="notice-list">
                    <div class="notice-item" v-for="(message, index) in noticesDataPublished" :key="index">
                        <div class="notice-title">{{ message.header }}</div>
                        <div class="notice-content">{{ message.content }}</div>
                        <div class="notice-footer">{{ formatDate(message.createtime || '') }}</div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 右侧：所有公告 -->
        <div class="notice-table-card">
            <div class="card-header">
                <span><t-icon name="root-list" /> 所有公告</span>
                <div class="actions">
                    <t-tooltip content="新增一条公告">
                        <t-button size="small" theme="default" @click="handleAddVisibleChange"><t-icon
                                name="add"></t-icon></t-button>
                    </t-tooltip>
                    <t-tooltip content="删除选中的公告">
                        <t-button size="small" theme="default" style="margin-left: 5px;"
                            @click="noticesStore.deleteSelectedData()"><t-icon name="delete"></t-icon></t-button>
                    </t-tooltip>
                </div>
            </div>
            <div class="card-body">
                <div class="table-search-bar">
                    <el-input v-model="noticesStore.search" size="small" placeholder="搜索" />
                </div>
                <t-card>
                    <el-table :data="filterTableData" height="340"
                        @selection-change="noticesStore.handleSelectionChange" stripe border>
                        <el-table-column type="selection" width="40" />
                        <el-table-column prop="header" label="标题" width="180"
                            show-overflow-tooltip></el-table-column>
                        <el-table-column prop="content" label="内容" width="220"
                            show-overflow-tooltip></el-table-column>
                        <el-table-column prop="createtime" label="创建时间" width="120" show-overflow-tooltip
                            column-key="createtime" sortable>
                            <template #default="scope">
                                {{ formatDate(scope.row.createtime) }}
                            </template>
                        </el-table-column>
                        <el-table-column prop="updatetime" label="更新时间" width="120" show-overflow-tooltip
                            column-key="updatetime" sortable>
                            <template #default="scope">
                                {{ formatDate(scope.row.updatetime) }}
                            </template>
                        </el-table-column>
                        <el-table-column prop="status" label="状态" width="80" :filters="[
                            { text: '已发布', value: 1 },
                            { text: '未发布', value: 0 },
                        ]"
                            :filter-method="(value: number, row: NoticesData) => { return row.status === value }"
                            filter-placement="bottom-end">
                            <template #default="scope">
                                <el-tag :type="scope.row.status == 1 ? 'success' : 'danger'" size="small">
                                    {{ scope.row.status == 1 ? '已发布' : '未发布' }}
                                </el-tag>
                            </template>
                        </el-table-column>
                        <el-table-column prop="author" label="作者" width="80"
                            show-overflow-tooltip></el-table-column>
                        <el-table-column fixed="right" min-width="120">
                            <template #default="scope">
                                <t-button size="small" theme="default"
                                    @click="() => { noticeEditFormData = scope.row; handleEditVisibleChange() }">编辑</t-button>
                                <t-button size="small" theme="danger" style="margin-left: 5px;"
                                    @click="noticesStore.deleteData([scope.row.id])">删除</t-button>
                            </template>
                        </el-table-column>
                    </el-table>
                    <t-pagination
                        v-model="noticesStore.currentPage"
                        v-model:pageSize="noticesStore.pageSize"
                        :total="noticesStore.data.length"
                        size="small"
                        theme="simple"
                        @page-size-change="onPageSizeChange"
                        @current-change="onCurrentChange"
                    />
                </t-card>
            </div>
        </div>

        <addNotice :visible="addVisible" @update:visible="handleAddVisibleChange" />
        <editNotice :visible="editVisible" @update:visible="handleEditVisibleChange" />
    </div>
</template>
<script setup lang="ts">
import { useNoticesStore,noticeEditFormData,deleteExpiredNoticesData,noticesDataPublished,filterTableData } from '@/stores/notices-store';
import { formatDate } from '@/utils/moment'
import addNotice from './noticesPage/addNotice.vue'
import editNotice from './noticesPage/editNotice.vue'
import { ref, onMounted } from 'vue';
import type{ NoticesData } from '@/types/noticesTypes'

const noticesStore = useNoticesStore()
const addVisible = ref(false)
const editVisible = ref(false)
const visible1 = ref(false)
const handleAddVisibleChange = () => {
    addVisible.value = !addVisible.value
}
const handleEditVisibleChange = () => {
    editVisible.value = !editVisible.value
}
const onPageSizeChange = (pageSize: number) => {
    noticesStore.pageSize = pageSize
    noticesStore.fetchPageData()
}
const onCurrentChange = (page: number) => {
    noticesStore.currentPage = page
    noticesStore.fetchPageData()
}
onMounted(() => {
    noticesStore.fetchPageData()
})
</script>
<style lang="scss" scoped>
.notices-page {
    display: flex;
    gap: 24px;
    
}

.notice-card, .notice-table-card {
    background: #fff;
    border-radius: 14px;
    box-shadow: 0 4px 24px rgba(0,0,0,0.08);
    padding: 0 0 24px 0;
    display: flex;
    flex-direction: column;
    flex: 1;
}

.notice-card {
    max-width: 420px;
    min-width: 320px;
}

.card-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 20px 24px 12px 24px;
    border-bottom: 1px solid #f0f0f0;
    font-size: 18px;
    font-weight: bold;
    color: #54a9ff;
    background: linear-gradient(90deg, #bef0ff 0%, #a5d9ff 100%);
    border-radius: 14px 14px 0 0;
}

.card-header .actions {
    display: flex;
    gap: 8px;
}

.card-body {
    padding: 16px 24px;
    flex: 1;
    overflow: auto;
}

.notice-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
    max-height: 65vh;
    overflow-y: auto;
}

.notice-item {
    background: #e6faff;
    border-radius: 8px;
    padding: 12px 16px;
    box-shadow: 0 2px 8px rgba(255,152,0,0.08);
    display: flex;
    flex-direction: column;
    gap: 4px;
}

.notice-item .notice-title {
    font-weight: 600;
    color: #0091ff;
}

.notice-item .notice-content {
    color: #333;
    font-size: 15px;
}

.notice-item .notice-footer {
    color: #999;
    font-size: 12px;
    text-align: right;
}

.notice-table-card {
    min-width: 0;
    flex: 2;
}

.table-search-bar {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 12px;
}

.table-search-bar .el-input {
    width: 200px;
}

.t-pagination {
    margin-top: 12px;
    text-align: right;
}

.t-button, .el-button {
    border-radius: 6px !important;
    transition: background 0.2s;
}

.t-button:hover, .el-button:hover {
    background: #b2e8ff !important;
    color: #0095ff !important;
}

</style>
  