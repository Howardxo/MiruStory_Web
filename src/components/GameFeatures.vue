<script setup lang="ts">
import { ref } from 'vue';
import FeatureItem from './reusable/FeatureItem.vue';

// Feature data
const features = [
  {
    id: 'spark-blessing',
    title: 'Spark Blessing',
    description: 'Enhance your character with celestial blessings that grant unique abilities and powerful buffs. Collect rare sparks from defeating legendary enemies and completing epic quests to unlock new tiers of blessings.',
    imagePath: '/features/spark-blessing.jpg'
  },
  {
    id: 'equipment-enchanting',
    title: 'Equipment Enchanting',
    description: 'Upgrade your weapons and armor through our advanced enchanting system. Apply magical properties to increase damage, defense, or add special effects like freezing, burning, or life-stealing abilities to dominate in combat.',
    imagePath: '/features/equipment-enchanting.jpg'
  },
  {
    id: 'equipment-synthesis',
    title: 'Equipment Synthesis',
    description: 'Combine multiple pieces of equipment to create powerful new items with enhanced stats and unique abilities. Discover rare recipes and gather exotic materials to craft legendary gear that will set you apart from other adventurers.',
    imagePath: '/features/equipment-synthesis.jpg'
  },
  {
    id: 'mount-awakening',
    title: 'Mount Awakening',
    description: 'Evolve your companions beyond their limits with the Mount Awakening system. Train and bond with your mounts to unlock their hidden potential, granting increased stats, special skills, and stunning visual transformations.',
    imagePath: '/features/mount-awakening.jpg'
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
      <h2 class="section-title">Game Features</h2>
      
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