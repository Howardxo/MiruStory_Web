<script setup lang="ts">
import { ref } from 'vue';
import FeatureItem from './reusable/FeatureItem.vue';

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
        <button 
          v-for="feature in features" 
          :key="feature.id"
          class="feature-tab" 
          :class="{ active: activeFeature.id === feature.id }"
          @click="setActiveFeature(feature)"
        >
          {{ feature.title }}
        </button>
      </div>
      
      <FeatureItem 
        :title="activeFeature.title"
        :description="activeFeature.description"
        :image-path="activeFeature.imagePath"
      />
    </div>
  </section>
</template>

<style scoped>
.game-features {
  background-color: var(--neutral-800);
  color: var(--text-light);
  min-height: 600px;
}

.section-title {
  color: var(--text-light);
}

.section-title:after {
  background-color: var(--primary-400);
}

.features-tabs {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: var(--space-2);
  margin-bottom: var(--space-4);
}

.feature-tab {
  background-color: transparent;
  color: var(--neutral-300);
  border: 1px solid var(--neutral-600);
  padding: var(--space-1) var(--space-2);
  border-radius: 8px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.feature-tab:hover {
  background-color: var(--primary-700);
  color: white;
  border-color: var(--primary-600);
}

.feature-tab.active {
  background-color: var(--primary-600);
  color: white;
  border-color: var(--primary-500);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

@media (max-width: 768px) {
  .features-tabs {
    flex-direction: column;
    align-items: center;
  }
  
  .feature-tab {
    width: 100%;
    max-width: 300px;
    text-align: center;
  }
}
</style>