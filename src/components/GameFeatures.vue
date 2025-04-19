<script setup lang="ts">
import { ref } from 'vue';
import FeatureItem from './reusable/FeatureItem.vue';
import TransitionExpand from './TransitionExpand.vue';

// Feature data
const features = [
  {
    id: 'spark-blessing',
    title: '星火祝福',
    description: '將裝備放在星火上,為你的角色帶來祝福,增加裝備的能力和威力,讓你在戰鬥中成為一名強大的勇士!',
    imagePath: 'https://drive.google.com/thumbnail?id=1pnPoIVQ8bYwlLdrtZaL1_jB_6gFv84dE&sz=w1200'
  },
  {
    id: 'equipment-enchanting',
    title: '點裝附魔',
    description: '再點裝使用寶石的能量,增加裝備的能力和威力,讓你在戰鬥中成為一名強大的勇士!',
    imagePath: 'https://drive.google.com/thumbnail?id=1YQNNXRmzhL6oXRuRJOAXhKz6711OzkKU&sz=w1200'
  },
  {
    id: 'equipment-synthesis',
    title: '騎寵覺醒',
    description: '使用覺醒石，讓你騎寵夥伴激發出來!',
    imagePath: 'https://drive.google.com/thumbnail?id=1jiY1_PNzxVBAtwCVQWb1n412B3dhPndd&sz=w1200'
  },
  {
    id: 'mount-awakening',
    title: '裝備合成',
    description: '各式裝備合成與進階，提升冒險者實力!',
    imagePath: 'https://drive.google.com/thumbnail?id=1mLGT9EITrYfgFNom9OUxoY0ATMiSsyBh&sz=w1200'
  }
];

const activeFeature = ref(features[0]);

const setActiveFeature = (feature: typeof features[0]) => {
  activeFeature.value = feature;
};
</script>

<template>
  <section class="section game-features">
    <div class="container">
      <h2 class="section-title">特色系統</h2>

      <div class="features-tabs">
        <button v-for="feature in features" :key="feature.id" class="feature-tab"
          :class="{ active: activeFeature.id === feature.id }" @click="setActiveFeature(feature)">
          {{ feature.title }}
        </button>
      </div>

      <TransitionExpand>
        <FeatureItem :key="activeFeature.id" class="feature-item" :title="activeFeature.title"
          :description="activeFeature.description" :image-path="activeFeature.imagePath" />
      </TransitionExpand>

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

/* 隱藏滾動條但保留功能 (對於 Webkit 瀏覽器) */
.features-tabs::-webkit-scrollbar {
  height: 4px;
  /* 滾動條高度 */
}

.features-tabs::-webkit-scrollbar-thumb {
  background-color: var(--primary-600);
  /* 滾動條顏色 */
  border-radius: 4px;
}

.feature-tab {
  /* 保持您現有的樣式 */
  flex: 0 0 auto;
  /* 防止按鈕被壓縮 */
}


@media (max-width: 768px) {
  .features-tabs {
    justify-content: flex-start;
    /* 在行動裝置上從左開始排列 */
    width: 100%;
    /* 確保寬度為 100% */
  }

  .feature-tab {
    width: 100px;
    /* 自動寬度而非 100% */
    max-width: none;
    /* 移除最大寬度限制 */
    text-align: center;
  }
}

@media (min-width: 768px) {
  .features-tabs {
    justify-content: center;
    /* 在行動裝置上從左開始排列 */
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