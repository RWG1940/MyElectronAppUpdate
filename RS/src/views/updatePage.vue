<template>
  <div class="wrap">
    <div class="header">
      <el-button type="primary" @click="showAddDialog = true">
        发布新版本
      </el-button>
    </div>

    <!-- Version Table -->
    <el-table :data="versionList" style="width: 100%">
      <el-table-column
        prop="versionName"
        label="版本号"
        show-overflow-tooltip
        sortable
      />
      <el-table-column
        prop="versionCode"
        label="版本代码"
        show-overflow-tooltip
        sortable
      />
      <el-table-column prop="updateContent" label="更新内容" />
      <el-table-column label="强制更新">
        <template #default="{ row }">
          {{ row.forceUpdate == 1 ? "是" : "否" }}
        </template>
      </el-table-column>
      <el-table-column
        prop="fileUrl"
        label="安装包地址"
        show-overflow-tooltip
      />
    </el-table>

    <el-pagination
      style="margin-top: 1%"
      background
      layout="prev, pager, next"
      :total="pagination.total"
      :page-size="pagination.pageSize"
      :current-page="pagination.current"
      @current-change="onPageChange"
    />

    <!-- Add Version Dialog -->
    <el-dialog
      v-model="showAddDialog"
      title="新版本发布"
      width="500px"
      style="border-radius: 15px"
    >
      <el-form :model="formData" label-width="100px">
        <el-form-item label="版本号">
          <el-input
            v-model="formData.versionName"
            placeholder="请输入版本号 (v1.0.0)"
          />
        </el-form-item>

        <el-form-item label="版本代码">
          <el-input-number v-model="formData.versionCode" :min="0" />
        </el-form-item>

        <el-form-item label="更新内容">
          <el-input
            type="textarea"
            v-model="formData.updateContent"
            placeholder="请输入更新内容"
          />
        </el-form-item>

        <el-form-item label="是否强制更新">
          <el-switch
            v-model="formData.forceUpdate"
            active-value=1
            inactive-value=0
          />
        </el-form-item>

        <el-form-item label="选择安装包">
          <input type="file" @change="handleFileChange" />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="showAddDialog = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">确定</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import {
  fetchUpdateDataPages,
  createNewVersion,
  uploadUpdateFile,
} from "@/api/services/update-api";
import { ElMessage } from "element-plus";

interface VersionItem {
  id: number;
  versionName: string;
  versionCode: number;
  updateContent: string;
  forceUpdate: boolean;
  fileUrl: string;
}

const versionList = ref<VersionItem[]>([]);
const pagination = reactive({ current: 1, pageSize: 10, total: 0 });
const showAddDialog = ref(false);
const file = ref<File | null>(null);

const formData = reactive({
  versionName: "",
  versionCode: null,
  updateContent: "",
  forceUpdate: null,
  fileUrl: "",
});

const getData = async () => {
  const res = await fetchUpdateDataPages(
    pagination.current,
    pagination.pageSize
  );
  const result = res.data.result;
  versionList.value = result.records || [];
  pagination.total = result.total || 0;
};

const onPageChange = (page: number) => {
  pagination.current = page;
  getData();
};

const handleFileChange = (e: Event) => {
  const target = e.target as HTMLInputElement;
  if (target.files && target.files.length > 0) {
    file.value = target.files[0];
  }
};

const handleSubmit = async () => {
  if (!file.value) {
    ElMessage.warning("请上传安装包");
    return;
  }

  // 上传文件
  const uploadRes = await uploadUpdateFile(file.value);
  const uploadedUrl = uploadRes.data.result.fileUrl;

  // 构造纯净对象（注意不能直接用 formData）
  const versionData = {
    versionName: formData.versionName,
    versionCode: formData.versionCode,
    updateContent: formData.updateContent,
    forceUpdate: formData.forceUpdate,
    fileUrl: uploadedUrl, // ✅ 关键
  };
  console.log("准备提交版本数据：", versionData);
  // 提交版本信息
  await createNewVersion(versionData);

  ElMessage.success("新版本发布成功");
  showAddDialog.value = false;
  getData();
};

onMounted(getData);
</script>

<style lang="scss" scoped>
.wrap {
  background-color: white;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  padding: 20px;
  height: 98%;

  .header {
    display: flex;
    justify-content: flex-end;
    margin-bottom: 20px;
  }
}

:deep(.el-button--primary) {
  background-color: #1e90ff;
  border-color: #1e90ff;
}
</style>
