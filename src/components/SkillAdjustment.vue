<script setup>
import { ref, onMounted } from 'vue';
import ClassItem from '../components/reusable/ClassItem.vue';
import TransitionExpand from '../components/TransitionExpand.vue';
import jobData from '../data/job.json'; // 直接導入JSON文件

// 使用ref建立響應式數據
const classes = ref([]);
const activeClass = ref(null);
const loading = ref(true);
const error = ref(null);

// 設置當前選中的職業
const setActiveClass = (characterClass) => {
  activeClass.value = characterClass;
};

// 組件掛載時直接設置數據
onMounted(() => {
  try {
    classes.value = jobData;
    if (jobData.length > 0) {
      activeClass.value = jobData[0];
    }
    loading.value = false;
  } catch (e) {
    error.value = e.message;
    loading.value = false;
    console.error('獲取職業數據時出錯:', e);
  }
});
</script>

<template>
  <section class="section skill-adjustment">
    <div class="container">
      <h2 class="section-title">技能調整</h2>

      <div v-if="loading" class="loading">加載中...</div>
      <div v-else-if="error" class="error">加載數據時出錯: {{ error }}</div>
      <div v-else>
        <div class="class-tabs">
          <button v-for="characterClass in classes" :key="characterClass.id" class="class-tab"
            :class="{ active: activeClass.id === characterClass.id }" @click="setActiveClass(characterClass)">
            {{ characterClass.title }}
          </button>
        </div>

        <TransitionExpand v-if="activeClass">
          <ClassItem :key="activeClass.id" :title="activeClass.title" :abilities="activeClass.abilities"
            :description="activeClass.description || ''" :image-path="activeClass.imagePath" />
        </TransitionExpand>
      </div>
    </div>
  </section>
</template>

<style scoped>
.skill-adjustment {
  background-color: var(--bg-light);
  color: var(--text-dark);
  min-height: 600px;
}

.class-tabs {
  display: flex;
  justify-content: flex-start;
  gap: var(--space-2);
  margin-bottom: var(--space-4);
  overflow-x: auto;
  white-space: nowrap;
  flex-wrap: nowrap;
  -webkit-overflow-scrolling: touch;
  padding-bottom: var(--space-2);

  /* Firefox 滾動條樣式 */
  scrollbar-width: thin;
  scrollbar-color: rgba(0, 0, 0, 0.1) transparent;

  /* Webkit 瀏覽器滾動條樣式 (Chrome, Safari, Edge等) */
  &::-webkit-scrollbar {
    height: 6px;
    /* 水平滾動條的高度 */
  }

  &::-webkit-scrollbar-track {
    background: transparent;
    /* 滾動條軌道背景透明 */
  }

  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.1);
    /* 淺灰色半透明滑塊 */
    border-radius: 6px;
    /* 圓角滑塊 */
  }

  &::-webkit-scrollbar-thumb:hover {
    background-color: rgba(0, 0, 0, 0.2);
    /* 懸停時稍微深一點 */
  }
}

.class-tab {
  background-color: var(--neutral-100);
  color: var(--neutral-700);
  border: 1px solid var(--neutral-300);
  padding: var(--space-1) var(--space-2);
  border-radius: 8px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  flex-shrink: 0;
  /* 防止選項被壓縮 */
}

.class-tab:hover {
  background-color: var(--primary-100);
  color: var(--primary-700);
  border-color: var(--primary-200);
}

.class-tab.active {
  background-color: var(--primary-500);
  color: white;
  border-color: var(--primary-600);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.loading,
.error {
  text-align: center;
  padding: var(--space-4);
  color: var(--text-dark);
}

.error {
  color: var(--error-500);
}

@media (max-width: 768px) {
  .class-tabs {
    justify-content: flex-start;
    /* 在小螢幕上保持左對齊 */
  }

  .class-tab {
    width: auto;
    /* 取消固定寬度限制 */
    max-width: none;
    /* 移除最大寬度限制 */
  }
}
</style>
