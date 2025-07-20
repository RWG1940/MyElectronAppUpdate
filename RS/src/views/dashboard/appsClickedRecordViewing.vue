<template>
  <div ref="echartRef" class="chart-box" />
</template>
<script setup lang="ts">
import * as echarts from "echarts";
import { ref, onMounted, nextTick } from "vue";
import { getButtonClickCount } from "@/api/services/dashboard-api"; // 假设你已有 API

const echartRef = ref<HTMLDivElement | null>(null);

// 定义 key 和显示名的映射
const labelMap: Record<string, string> = {
  openInChrome: "浏览器打开",
  yjyh: "一键优化",
  optimizeSelected: "选中优化",
  handleLoginBtn: "登录操作",
  optimizeNetwork: "网络优化",
  fixPrinter: "打印机修复",
  "domain-expiry": "域名到期",
  emailQuery: "邮件查询",
};
// 原始 key 列表
const featureKeys = Object.keys(labelMap);
const renderChart = async () => {
  const dataPromises = featureKeys.map(async (key) => {
    const res = await getButtonClickCount(key);
    return {
      name: labelMap[key], // 使用自定义名称
      value: res?.data?.result || 0,
    };
  });

  const result = await Promise.all(dataPromises);
  const chart = echarts.init(echartRef.value!);

  chart.setOption({
    title: {
      text: "按钮点击占比",
      left: "center",
      textStyle: {
        fontSize: 16,
        color: "#2196f3",
      },
    },
    tooltip: {
      trigger: "item",
      formatter: "{b}: {c} ({d}%)",
    },
    legend: {
      orient: "vertical",
      left: "left",
      data: result.map((r) => r.name),
    },
    series: [
      {
        name: "点击次数",
        type: "pie",
        radius: "55%",
        center: ["50%", "60%"],
        data: result,
        emphasis: {
          itemStyle: {
            shadowBlur: 10,
            shadowOffsetX: 0,
            shadowColor: "rgba(0, 0, 0, 0.5)",
          },
        },
        label: {
          formatter: "{b}: {d}%",
        },
      },
    ],
  });

  window.addEventListener("resize", () => {
    chart.resize();
  });
};

onMounted(async () => {
  await nextTick();
  renderChart();
});
</script>
<style scoped>
.chart-box {
  width: 100%;
  height: 260px;
  padding: 12px 0;
}
</style>
