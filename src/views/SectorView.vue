<!-- src/views/SectorView.vue -->
<template>
  <div class="sector-view">
    <div class="page-header">
      <h2>{{ $t('nav.sector') }}</h2>
      <div class="sector-controls">
        <input 
          type="text" 
          v-model="searchKeyword" 
          :placeholder="$t('sector.searchPlaceholder')"
          class="search-input"
        />
        <button @click="sortSectors('change')" class="btn">
          {{ sortAsc ? '↑' : '↓' }} {{ $t('sector.sortByChange') }}
        </button>
      </div>
    </div>

    <div v-if="loading" class="loading">加载板块数据中...</div>
    <div v-else-if="!filteredSectors.length" class="empty-state">
      暂无匹配的板块数据
    </div>
    <div v-else class="sectors-grid">
      <div 
        v-for="sector in filteredSectors" 
        :key="sector.id" 
        class="sector-card card-hover"
      >
        <h3>{{ sector.name }}</h3>
        <div class="sector-change" :class="{ up: sector.change >= 0, down: sector.change < 0 }">
          {{ sector.change >= 0 ? '▲' : '▼' }} {{ Math.abs(sector.change).toFixed(2) }}%
        </div>
        <div class="sector-volume">
          成交量: {{ (sector.volume / 100000000).toFixed(2) }}亿
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { fetchSectorData } from '@/utils/api';

const loading = ref(true);
const searchKeyword = ref('');
const sortAsc = ref(true);
const sectors = ref<any[]>([]);

// 筛选+排序后的板块数据
const filteredSectors = computed(() => {
  let result = [...sectors.value];
  // 搜索筛选
  if (searchKeyword.value) {
    result = result.filter(sector => 
      sector.name.toLowerCase().includes(searchKeyword.value.toLowerCase())
    );
  }
  // 排序
  result.sort((a, b) => {
    return sortAsc.value ? a.change - b.change : b.change - a.change;
  });
  return result;
});

// 排序切换
const sortSectors = () => {
  sortAsc.value = !sortAsc.value;
};

// 加载真实板块数据
onMounted(async () => {
  try {
    loading.value = true;
    const data = await fetchSectorData(); // 后续改造该方法接入真实API
    sectors.value = data;
  } catch (error) {
    console.error('加载板块数据失败:', error);
    sectors.value = [];
  } finally {
    loading.value = false;
  }
});
</script>

<style scoped>
.sector-view {
  padding: 2rem;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
  flex-wrap: wrap;
  gap: 1rem;
}

.sector-controls {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.search-input {
  padding: 0.4rem 0.8rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: var(--card-bg);
  color: var(--text-primary);
}

.sectors-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

.sector-card {
  padding: 1.2rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  background: var(--card-bg);
}

.sector-card h3 {
  margin: 0.5rem 0 1rem;
  font-size: 1.1rem;
}

.sector-change {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.sector-volume {
  font-size: 0.9rem;
  color: #666;
}

.up { color: #28a745; }
.down { color: #dc3545; }

.loading, .empty-state {
  text-align: center;
  padding: 3rem;
  color: var(--text-primary);
  font-size: 1rem;
}
</style>