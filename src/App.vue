<template>
  <div id="app" :data-theme="theme">
    <Header />
    <transition name="fade">
      <router-view />
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { APP_CONFIG } from '@/config';
import Header from '@/components/layout/Header.vue';

const theme = ref(APP_CONFIG.theme.default);

onMounted(() => {
  const saved = localStorage.getItem(APP_CONFIG.theme.storageKey);
  if (saved) theme.value = saved;
  else localStorage.setItem(APP_CONFIG.theme.storageKey, theme.value);
});
</script>

<style>
html, body, #app {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

:root {
  --bg-primary: #ffffff;
  --text-primary: #333333;
  --card-bg: #f8f9fa;
}

[data-theme="dark"] {
  --bg-primary: #121212;
  --text-primary: #e0e0e0;
  --card-bg: #1e1e1e;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  background: var(--bg-primary);
  color: var(--text-primary);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background: var(--bg-primary);
  color: var(--text-primary);
}

#app > :not(.fade-enter-from) {
  flex: 1;
}

/* 滚动条美化 */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}
::-webkit-scrollbar-track {
  background: var(--card-bg);
  border-radius: 4px;
}
::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover {
  background: #999;
}

/* 通用过渡动画 */
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

/* 按钮/输入框通用样式 */
button, .btn {
  background: var(--card-bg);
  color: var(--text-primary);
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 0.4rem 0.8rem;
  cursor: pointer;
  transition: all 0.2s ease;
}
button:hover, .btn:hover {
  border-color: #007bff;
  background: #f0f7ff;
  color: #007bff;
}
button:active, .btn:active {
  transform: scale(0.98);
}

/* 卡片悬浮效果 */
.card-hover {
  transition: box-shadow 0.3s ease, transform 0.2s ease;
}
.card-hover:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.1);
  transform: translateY(-2px);
}
</style>