<!-- src/views/FundsView.vue -->
<template>
  <div class="funds-view">
    <div class="page-header">
      <h2>{{ $t('nav.funds') }}</h2>
      <button @click="sortFunds('flow')" class="btn">
        {{ sortAsc ? '↑' : '↓' }} 按资金流向排序
      </button>
    </div>
    
    <!-- 加载状态 -->
    <div v-if="loading" class="loading">加载资金数据中...</div>
    
    <!-- 空数据状态 -->
    <div v-else-if="!fundFlowData.length" class="empty-state">
      暂无资金流向数据，请稍后重试
    </div>
    
    <!-- 表格 -->
    <table v-else class="funds-table card-hover">
      <thead>
        <tr>
          <th>股票代码</th>
          <th>股票名称</th> <!-- 新增：显示真实股票名称 -->
          <th>资金流向 (亿)</th>
          <th>趋势</th>
          <th>更新时间</th> <!-- 新增：数据更新时间 -->
        </tr>
      </thead>
      <tbody>
        <tr v-for="fund in fundFlowData" :key="fund.symbol" class="row-hover">
          <td>{{ fund.symbol }}</td>
          <td>{{ fund.name }}</td>
          <td>{{ (fund.flow / 100000000).toFixed(2) }}</td>
          <td :class="{ up: fund.flow > 0, down: fund.flow < 0 }">
            {{ fund.flow > 0 ? '▲' : '▼' }}
          </td>
          <td>{{ formatTime(fund.updatedAt) }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { fetchFundsFlow } from '@/utils/api'; // 复用原有API方法，后续改造该方法接入真实数据

const loading = ref(true);
const sortAsc = ref(true);
const rawFundFlowData = ref<any[]>([]);

// 排序后的资金数据
const fundFlowData = computed(() => {
  return [...rawFundFlowData.value].sort((a, b) => {
    return sortAsc.value ? a.flow - b.flow : b.flow - a.flow;
  });
});

// 排序切换
const sortFunds = () => {
  sortAsc.value = !sortAsc.value;
};

// 格式化时间
const formatTime = (time: string) => {
  return new Date(time).toLocaleString();
};

// 加载真实资金流数据
onMounted(async () => {
  try {
    loading.value = true;
    const data = await fetchFundsFlow(); // 后续改造该方法接入真实API
    rawFundFlowData.value = data;
  } catch (error) {
    console.error('加载资金数据失败:', error);
    rawFundFlowData.value = [];
  } finally {
    loading.value = false;
  }
});
</script>

<style scoped>
.funds-view {
  padding: 2rem;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.funds-table {
  width: 100%;
  border-collapse: collapse;
  border-radius: 8px;
  overflow: hidden;
  background: var(--card-bg);
}

.funds-table th,
.funds-table td {
  padding: 0.75rem;
  border-bottom: 1px solid #ddd;
  text-align: left;
}

.funds-table th {
  background: var(--card-bg);
  font-weight: bold;
  background-color: #f8f9fa;
}

/* 行hover效果 */
.row-hover:hover {
  background-color: #f0f7ff;
}

.up { color: #28a745; }
.down { color: #dc3545; }

/* 加载/空数据样式 */
.loading, .empty-state {
  text-align: center;
  padding: 3rem;
  color: var(--text-primary);
  font-size: 1rem;
}
</style>