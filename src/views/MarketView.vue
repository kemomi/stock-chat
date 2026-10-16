<!-- src/views/MarketView.vue -->
<template>
  <div class="market-view">
    <h2>{{ $t('nav.market') }}</h2>
    <div v-if="store.loading" class="loading">加载市场数据中...</div>
    <div v-else>
      <div class="market-meta">
        最后更新: {{ lastUpdated }} | 实时数据: {{ store.realTimeData.length }}条
      </div>
      <div class="indices-list">
        <div 
          v-for="index in indices" 
          :key="index.symbol" 
          class="index-item card-hover"
        >
          <div class="index-info">
            <h3>{{ index.name }}</h3>
            <span class="index-symbol">{{ index.symbol }}</span>
          </div>
          <div class="index-price">{{ index.price.toFixed(2) }}</div>
          <div class="index-change" :class="{ up: index.changePercent >= 0, down: index.changePercent < 0 }">
            {{ index.changePercent >= 0 ? '▲' : '▼' }} {{ Math.abs(index.changePercent).toFixed(2) }}%
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref, watch } from 'vue';
import { useMarketStore } from '@/stores/marketStore';
import { APP_CONFIG } from '@/config';

const store = useMarketStore();
const lastUpdated = ref(new Date().toLocaleString());

const indices = computed(() =>
  APP_CONFIG.globalIndices.map(idx => ({
    ...idx,
    price: store.getIndexPrice(idx.symbol),
    changePercent: store.getIndexChange(idx.symbol),
  }))
);

onMounted(() => {
  store.loadIndices();
  // 监听实时数据更新，更新时间戳
  watch(store.realTimeData, () => {
    lastUpdated.value = new Date().toLocaleString();
  }, { deep: true });
});
</script>

<style scoped>
.market-view {
  padding: 2rem;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: var(--text-primary);
}

.market-meta {
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 1rem;
  padding: 0.5rem;
  background: var(--card-bg);
  border-radius: 6px;
}

.indices-list {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
}

.index-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  border-radius: 8px;
  border: 1px solid #ddd;
  background: var(--card-bg);
}

.index-info {
  flex: 1;
}

.index-info h3 {
  margin: 0;
  font-size: 1.1rem;
}

.index-symbol {
  font-size: 0.8rem;
  color: #666;
  margin-left: 0.5rem;
}

.index-price {
  flex: 0 0 100px;
  text-align: right;
  font-size: 1.1rem;
  font-weight: 500;
}

.index-change {
  flex: 0 0 80px;
  text-align: right;
  font-weight: bold;
}

.up { color: #28a745; }
.down { color: #dc3545; }
</style>