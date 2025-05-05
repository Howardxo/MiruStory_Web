<script setup>
import { ref, onMounted } from 'vue';
import FeatureItem from './reusable/FeatureItem.vue';
import TransitionExpand from './TransitionExpand.vue';
import featuresData from '../data/features.json'; // 使用相對路徑，避免別名問題

// 使用ref建立響應式數據
const features = ref([]);
const activeFeature = ref(null);
const loading = ref(true);
const error = ref(null);

// 設置當前選中的特色
const setActiveFeature = (feature) => {
  activeFeature.value = feature;
};

// 組件掛載時設置數據
onMounted(() => {
  try {
    features.value = featuresData;
    if (featuresData.length > 0) {
      activeFeature.value = featuresData[0];
    }
    loading.value = false;
  } catch (e) {
    error.value = e?.message || "未知錯誤";
    loading.value = false;
    console.error('獲取特色數據時出錯:', e);
  }
});
</script>

<template>
  <section class="section game-features">
    <div class="container">
      <h2 class="section-title">特色系統</h2>

      <div v-if="loading" class="loading">加載中...</div>
      <div v-else-if="error" class="error">加載數據時出錯: {{ error }}</div>
      <div v-else>
        <div class="features-tabs">
          <button v-for="feature in features" :key="feature.id" class="feature-tab"
            :class="{ active: activeFeature?.id === feature.id }" @click="setActiveFeature(feature)">
            {{ feature.title }}
          </button>
        </div>

        <TransitionExpand v-if="activeFeature">
          <FeatureItem :key="activeFeature.id" class="feature-item" :title="activeFeature.title"
            :description="activeFeature.description" :image-path="activeFeature.imagePath" />
        </TransitionExpand>
      </div>
    </div>
  </section>
</template>

<style scoped>
.game-features {
  background-color: var(--neutral-800);
  color: var(--text-light);
  position: relative;
}

.section-title {
  color: var(--text-light);
}

.section-title:after {
  background-color: var(--primary-400);
}

.features-tabs {
  display: flex;
  justify-content: center;
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

  /* Webkit 瀏覽器滾動條樣式 */
  &::-webkit-scrollbar {
    height: 6px;
  }

  &::-webkit-scrollbar-track {
    background: transparent;
  }

  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.1);
    border-radius: 6px;
  }

  &::-webkit-scrollbar-thumb:hover {
    background-color: rgba(0, 0, 0, 0.2);
  }
}

/* 隱藏滾動條但保留功能 (對於 Webkit 瀏覽器) */
.features-tabs::-webkit-scrollbar {
  height: 4px;
}

.features-tabs::-webkit-scrollbar-thumb {
  background-color: var(--primary-600);
  border-radius: 4px;
}

.feature-tab {
  flex: 0 0 auto;
}

.loading,
.error {
  text-align: center;
  padding: var(--space-4);
  color: var(--text-light);
}

.error {
  color: var(--error-500);
}

@media (max-width: 768px) {
  .features-tabs {
    justify-content: flex-start;
    width: 100%;
  }

  .feature-tab {
    width: 100px;
    max-width: none;
    text-align: center;
  }
}

@media (min-width: 768px) {
  .features-tabs {
    justify-content: center;
  }

  .feature-item {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .game-features {
    height: auto;
  }
}
</style>
