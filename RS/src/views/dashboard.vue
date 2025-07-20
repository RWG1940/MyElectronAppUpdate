<template>
  <div class="dashboard-page">
    <!-- 数据统计卡片区 -->
    <div class="dashboard-cards">
      <div class="stat-card">
        <div class="stat-icon" style="background: linear-gradient(135deg,#64b5f6,#1976d2)">
          <t-icon name="chart-bar" size="32" />
        </div>
        <div class="stat-info">
          <div class="stat-title">今日访问</div>
          <div class="stat-value">{{ nowRecord }}</div>  <!-- 动态显示 yjyhRecord -->
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon" style="background: linear-gradient(135deg,#b3e5fc,#0288d1)">
          <t-icon name="laptop" size="32" />
        </div>
        <div class="stat-info">
          <div class="stat-title">装机量</div>
          <div class="stat-value">{{ pcRecord }}</div>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon" style="background: linear-gradient(135deg,#bbdefb,#2196f3)">
          <t-icon name="usergroup" size="32" />
        </div>
        <div class="stat-info">
          <div class="stat-title">在线用户</div>
          <div class="stat-value">null</div>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon" style="background: linear-gradient(135deg,#e3f2fd,#1565c0)">
          <t-icon name="notification" size="32" />
        </div>
        <div class="stat-info">
          <div class="stat-title">系统消息</div>
          <div class="stat-value">null</div>
        </div>
      </div>
    </div>

    <!-- 趋势图表区 -->
    <div class="dashboard-charts">
      <t-card class="chart-card">
        <div class="chart-title">
          <appsClickedRecordViewing />
        </div>
      </t-card>
      <t-card class="chart-card">
        <div class="chart-title">部门电脑问题统计</div>
      </t-card>
    </div>

    <!-- 待办事项区 -->
    <div class="dashboard-todo">
      <t-card>
        <div class="todo-title">待办事项</div>
        <ul class="todo-list">
          <li>
            <t-icon name="check-circle" style="color: #52c41a;" />
            <span class="done">xxx电脑c盘存储空间不足</span>
          </li>
          <li>
            <t-icon name="error-circle" style="color: #ff9800;" />
            <span>xxx域名到期</span>
          </li>
        </ul>
      </t-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import {getButtonClickCount} from '@/api/services/dashboard-api';
import { usePcInfoStore } from '@/stores/pcInfo-store';
import appsClickedRecordViewing from './dashboard/appsClickedRecordViewing.vue';

// 定义一个变量来存储 今日访问 记录的数量
const nowRecord = ref<number>(0);
const pcRecord = ref<number>(0);
// 在组件挂载后调用后端接口
onMounted(async () => {
  try {
    // 调用后端接口获取 now 记录数量
    const response = await getButtonClickCount('index');
    if (response.data && response.data.result) {
      nowRecord.value = response.data.result;
    } else {
      console.error('获取 今日访问 记录数量失败:', response.data);
    }
  } catch (error) {
    console.error('请求失败:', error);
  }
  try {
    // 获取 PC 信息
    const pcInfoStore = usePcInfoStore();
    await pcInfoStore.fetchPageData();
    pcRecord.value = pcInfoStore.pageDataSum; // 假设 pcInfo 是一个数组，记录数量即为数组长度
  } catch (error) {
    console.error('获取 PC 信息失败:', error);
  }
});
</script>


<style scoped>
.dashboard-page {
  padding: 32px 24px;
  background: #f6f8fa;
  border-radius: 10px;
  min-height: 100vh;
}
.dashboard-header {
  margin-bottom: 24px;
}
.welcome {
  display: flex;
  align-items: center;
  gap: 18px;
  background: linear-gradient(90deg,#e3f0ff 0%,#f0f6ff 100%);
  border-radius: 12px;
  padding: 24px 32px;
  box-shadow: 0 2px 12px rgba(33,150,243,0.08);
}
.welcome-img {
  width: 64px;
  height: 64px;
}
.dashboard-cards {
  display: flex;
  gap: 24px;
  margin-bottom: 24px;
}
.stat-card {
  flex: 1;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(33,150,243,0.06);
  display: flex;
  align-items: center;
  padding: 20px 24px;
  gap: 18px;
  transition: box-shadow 0.2s;
}
.stat-card:hover {
  box-shadow: 0 4px 24px rgba(33,150,243,0.12);
}
.stat-icon {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 28px;
}
.stat-info {
  flex: 1;
}
.stat-title {
  font-size: 15px;
  color: #2196f3;
}
.stat-value {
  font-size: 24px;
  font-weight: bold;
  color: #222;
}
.dashboard-charts {
  display: flex;
  gap: 24px;
  margin-bottom: 24px;
}
.chart-card {
  flex: 1;
  min-width: 0;
  padding: 0;
}
.chart-title {
  font-size: 16px;
  font-weight: bold;
  color: #2196f3;
  margin: 18px 0 8px 18px;
}
.chart-img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  border-radius: 8px;
}
.dashboard-todo {
  max-width: 480px;
}
.todo-title {
  font-size: 16px;
  font-weight: bold;
  color: #2196f3;
  margin-bottom: 12px;
}
.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.todo-list li {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 15px;
  color: #333;
}
.todo-list .done {
  text-decoration: line-through;
  color: #bbb;
}
</style>
