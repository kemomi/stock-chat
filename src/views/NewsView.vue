<!-- src/views/NewsView.vue -->
<template>
  <div class="news-view">
    <h2>{{ $t('nav.market') }} - 新闻</h2>
    <div v-if="loading" class="loading">加载中...</div>
    <div v-else class="news-list">
      <article v-for="(article, idx) in articles" :key="idx" class="news-item">
        <h3>{{ article.title }}</h3>
        <p>{{ article.description }}</p>
        <div class="meta">
          <span class="source">{{ article.source?.name }}</span>
          <span class="time">{{ new Date(article.publishedAt).toLocaleDateString() }}</span>
        </div>
        <a :href="article.url" target="_blank" class="read-more">阅读全文</a>
      </article>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { fetchMarketNews } from '@/utils/api';

const loading = ref(true);
const articles = ref<any[]>([]);

onMounted(async () => {
  try {
    const data = await fetchMarketNews('zh');
    articles.value = data.articles?.slice(0, 10) || [];
  } catch (error) {
    console.error('Failed to fetch news:', error);
    articles.value = [];
  } finally {
    loading.value = false;
  }
});
</script>

<style scoped>
.news-view {
  padding: 2rem;
}

.loading {
  text-align: center;
  padding: 2rem;
}

.news-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.news-item {
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  background: var(--card-bg);
}

.news-item h3 {
  margin: 0 0 0.5rem 0;
  color: var(--text-primary);
}

.news-item p {
  color: var(--text-primary);
  line-height: 1.6;
  margin-bottom: 0.75rem;
}

.meta {
  font-size: 0.875rem;
  color: #999;
  margin-bottom: 0.75rem;
}

.source::after {
  content: ' | ';
  margin: 0 0.25rem;
}

.read-more {
  display: inline-block;
  color: #007bff;
  text-decoration: none;
  font-weight: 500;
}

.read-more:hover {
  text-decoration: underline;
}
</style>