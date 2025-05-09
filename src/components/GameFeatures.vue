<script setup>
import { ref, onMounted, computed, nextTick, watch } from 'vue';
import FeatureItem from './reusable/FeatureItem.vue';
import TransitionExpand from './TransitionExpand.vue';
import featuresData from '../data/features.json';

const features = ref([]);
const activeFeatureIndex = ref(0);
const previousIndex = ref(0);
const loading = ref(true);
const error = ref(null);

// 動畫控制
const isVisible = ref(false);
const tabsAnimated = ref(false);
const tabIndicatorPos = ref({ left: 0, width: 0 });
const featureRefs = ref([]);
const sectionRef = ref(null);

// 計算當前選中的特色
const activeFeature = computed(() =>
  activeFeatureIndex.value !== null ? features.value[activeFeatureIndex.value] : null
);

// 設置當前選中的特色並更新指示器
const setActiveFeature = async (index) => {
  if (index === activeFeatureIndex.value) return;

  previousIndex.value = activeFeatureIndex.value;
  activeFeatureIndex.value = index;

  // 添加動畫延遲，使轉場更流暢
  await nextTick();
  updateTabIndicator(index);

  // 添加觸發動畫效果的類
  if (featureRefs.value && featureRefs.value[index]) {
    featureRefs.value.forEach(tab => {
      tab.classList.remove('pulse-animation');
    });
    featureRefs.value[index].classList.add('pulse-animation');
  }
};

// 更新選項卡指示器位置
const updateTabIndicator = (index) => {
  if (!featureRefs.value || !featureRefs.value[index]) return;

  const tab = featureRefs.value[index];
  tabIndicatorPos.value = {
    left: tab.offsetLeft,
    width: tab.offsetWidth
  };
};

// 觀察元素進入視圖
const observeIntersection = () => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        isVisible.value = true;
        setTimeout(() => { tabsAnimated.value = true; }, 300);
        observer.disconnect(); // 只觸發一次
      }
    });
  }, { threshold: 0.1 });

  if (sectionRef.value) {
    observer.observe(sectionRef.value);
  }
};

// 初始化特效和自動循環展示功能
const initFeatureEffects = () => {
  // 初始化選項卡指示器位置
  nextTick(() => {
    updateTabIndicator(activeFeatureIndex.value);

    // 創建自動循環展示功能（可選）
    let autoChangeInterval;
    const startAutoChange = () => {
      autoChangeInterval = setInterval(() => {
        const nextIndex = (activeFeatureIndex.value + 1) % features.value.length;
        setActiveFeature(nextIndex);
      }, 8000); // 每8秒切換一次
    };

    const stopAutoChange = () => {
      clearInterval(autoChangeInterval);
    };

    // 啟動自動循環
    startAutoChange();

    // 用戶互動時暫停自動循環
    const container = document.querySelector('.game-features');
    if (container) {
      container.addEventListener('mouseenter', stopAutoChange);
      container.addEventListener('touchstart', stopAutoChange, { passive: true });
      container.addEventListener('mouseleave', startAutoChange);
      container.addEventListener('touchend', startAutoChange, { passive: true });
    }
  });
};

// 監聽activeFeatureIndex的變化，添加動畫效果
watch(activeFeatureIndex, () => {
  // 這裡可以添加額外的動畫邏輯
}, { immediate: false });

// 組件掛載時設置數據
onMounted(async () => {
  try {
    // 模擬數據加載延遲（實際項目中可能不需要）
    await new Promise(r => setTimeout(r, 300));

    features.value = featuresData;
    if (featuresData.length > 0) {
      activeFeatureIndex.value = 0;
      previousIndex.value = 0;
    }
    loading.value = false;

    // 設定元素引用後進行初始化
    await nextTick();
    observeIntersection();
    initFeatureEffects();

  } catch (e) {
    error.value = e?.message || "未知錯誤";
    loading.value = false;
    console.error('獲取特色數據時出錯:', e);
  }
});
</script>

<template>
  <section ref="sectionRef" class="section game-features" :class="{ 'is-visible': isVisible }">
    <div class="parallax-bg"></div>

    <div class="container">
      <h2 class="section-title" :class="{ 'slide-in': isVisible }">
        <span class="title-decoration left"></span>
        特色系統
        <span class="title-decoration right"></span>
      </h2>

      <div v-if="loading" class="loading">
        <div class="loading-spinner"></div>
        <div class="loading-text">正在載入遊戲特色...</div>
      </div>

      <div v-else-if="error" class="error">
        <i class="error-icon">⚠️</i>
        <div>加載數據時出錯: {{ error }}</div>
      </div>

      <div v-else class="features-content">
        <div class="features-tabs-container" :class="{ 'animated': tabsAnimated }">
          <div class="features-tabs">
            <button v-for="(feature, index) in features" :key="feature.id" class="feature-tab"
              :class="{ 'active': activeFeatureIndex === index }" @click="setActiveFeature(index)"
              ref="el => { if (el) featureRefs[index] = el }">
              <span class="tab-icon" v-if="feature.icon">{{ feature.icon }}</span>
              <span class="tab-text">{{ feature.title }}</span>
              <span class="tab-highlight"></span>
            </button>

            <!-- 活動選項卡指示器 -->
            <div class="tab-indicator" :style="{
              left: `${tabIndicatorPos.left}px`,
              width: `${tabIndicatorPos.width}px`
            }"></div>
          </div>
        </div>

        <TransitionExpand :active-index="activeFeatureIndex">
          <div v-if="activeFeature" :key="activeFeature.id" class="feature-content-wrapper">
            <FeatureItem class="feature-item" :title="activeFeature.title" :description="activeFeature.description"
              :image-path="activeFeature.imagePath" />

            <!-- 裝飾元素 -->
            <div class="feature-decorations">
              <div class="decoration-item top-left"></div>
              <div class="decoration-item top-right"></div>
              <div class="decoration-item bottom-left"></div>
              <div class="decoration-item bottom-right"></div>
            </div>
          </div>
        </TransitionExpand>

        <div class="navigation-dots">
          <button v-for="(_, index) in features" :key="index" class="dot"
            :class="{ 'active': activeFeatureIndex === index }" @click="setActiveFeature(index)"></button>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.game-features {
  background-color: var(--neutral-800);
  color: var(--text-light);
  position: relative;
  overflow: hidden;
  transition: all 0.5s ease-out;
  padding: 5rem 0;
}

.parallax-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(225deg,
      rgba(40, 44, 52, 0.8),
      rgba(17, 17, 20, 0.95));
  z-index: -1;
  transition: transform 0.4s ease-out;
}

.game-features:hover .parallax-bg {
  transform: translateY(-5px);
}

.container {
  position: relative;
  z-index: 2;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1.5rem;
}

/* 標題樣式 */
.section-title {
  color: var(--text-light);
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 3rem;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s cubic-bezier(0.17, 0.67, 0.83, 0.67);
}

.section-title.slide-in {
  opacity: 1;
  transform: translateY(0);
}

.title-decoration {
  height: 3px;
  width: 0;
  background: linear-gradient(90deg, transparent, var(--primary-400), transparent);
  transition: width 0.8s ease-out 0.3s;
}

.section-title.slide-in .title-decoration {
  width: 60px;
}

.title-decoration.left {
  transform-origin: right;
}

.title-decoration.right {
  transform-origin: left;
}

/* 加載狀態 */
.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 3rem;
  height: 300px;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 4px solid rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  border-top-color: var(--primary-400);
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

.loading-text {
  color: var(--text-light);
  font-size: 1.1rem;
  animation: pulse 1.5s infinite alternate;
}

/* 錯誤狀態 */
.error {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 3rem;
  color: var(--error-500);
  background: rgba(255, 0, 0, 0.1);
  border-radius: 8px;
  border: 1px solid var(--error-700);
}

.error-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  animation: shake 0.5s ease-in-out;
}

/* 特色選項卡樣式 */
.features-content {
  opacity: 0;
  transform: translateY(20px);
  animation: fadeInUp 0.8s ease-out 0.3s forwards;
}

.features-tabs-container {
  position: relative;
  margin-bottom: 3rem;
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.5s ease-out;
}

.features-tabs-container.animated {
  opacity: 1;
  transform: translateY(0);
}

.features-tabs {
  display: flex;
  justify-content: center;
  gap: 0.75rem;
  margin-bottom: 1.5rem;
  overflow-x: auto;
  white-space: nowrap;
  flex-wrap: nowrap;
  -webkit-overflow-scrolling: touch;
  padding: 0.75rem 0.5rem;
  position: relative;

  /* 自定義滾動條 */
  scrollbar-width: thin;
  scrollbar-color: var(--primary-600) transparent;
}

/* Webkit 滾動條樣式 */
.features-tabs::-webkit-scrollbar {
  height: 5px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 5px;
}

.features-tabs::-webkit-scrollbar-thumb {
  background-color: var(--primary-600);
  border-radius: 5px;
  transition: all 0.3s;
}

.features-tabs::-webkit-scrollbar-thumb:hover {
  background-color: var(--primary-400);
}

.tab-indicator {
  position: absolute;
  bottom: 0;
  height: 3px;
  background: linear-gradient(90deg, var(--primary-500), var(--primary-300));
  border-radius: 3px;
  transition: left 0.3s ease, width 0.3s ease;
}

.feature-tab {
  position: relative;
  flex: 0 0 auto;
  background-color: rgba(255, 255, 255, 0.08);
  color: var(--text-light);
  border: none;
  border-radius: 8px;
  padding: 0.8rem 1.25rem;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  overflow: hidden;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.feature-tab:hover {
  background-color: rgba(255, 255, 255, 0.12);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.feature-tab.active {
  background-color: rgba(var(--primary-rgb), 0.2);
  color: var(--primary-300);
  font-weight: 600;
}

.tab-icon {
  display: inline-block;
  margin-right: 0.5rem;
  font-size: 1.1rem;
}

.tab-text {
  position: relative;
  z-index: 2;
}

.tab-highlight {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at center, rgba(var(--primary-rgb), 0.2) 0%, transparent 70%);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.feature-tab:hover .tab-highlight {
  opacity: 1;
}

/* 特色內容樣式 */
.feature-content-wrapper {
  position: relative;
  padding: 1.5rem;
  border-radius: 12px;
  background-color: rgba(255, 255, 255, 0.03);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(8px);
  overflow: hidden;
  min-height: 350px;
  transition: all 0.5s ease;
}

.feature-content-wrapper:hover {
  background-color: rgba(255, 255, 255, 0.05);
  transform: translateY(-5px);
}

/* 裝飾元素 */
.feature-decorations {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.decoration-item {
  position: absolute;
  width: 50px;
  height: 50px;
  opacity: 0.3;
  transition: all 0.8s ease;
}

.decoration-item.top-left {
  top: 10px;
  left: 10px;
  border-top: 2px solid var(--primary-400);
  border-left: 2px solid var(--primary-400);
  animation: pulseDecoration 3s infinite alternate;
}

.decoration-item.top-right {
  top: 10px;
  right: 10px;
  border-top: 2px solid var(--primary-400);
  border-right: 2px solid var(--primary-400);
  animation: pulseDecoration 3s infinite alternate 1s;
}

.decoration-item.bottom-left {
  bottom: 10px;
  left: 10px;
  border-bottom: 2px solid var(--primary-400);
  border-left: 2px solid var(--primary-400);
  animation: pulseDecoration 3s infinite alternate 0.5s;
}

.decoration-item.bottom-right {
  bottom: 10px;
  right: 10px;
  border-bottom: 2px solid var(--primary-400);
  border-right: 2px solid var(--primary-400);
  animation: pulseDecoration 3s infinite alternate 1.5s;
}

.feature-content-wrapper:hover .decoration-item {
  opacity: 0.6;
  width: 70px;
  height: 70px;
}

/* 導航點 */
.navigation-dots {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-top: 2.5rem;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.2);
  border: none;
  cursor: pointer;
  padding: 0;
  transition: all 0.3s ease;
}

.dot:hover {
  background-color: rgba(255, 255, 255, 0.4);
}

.dot.active {
  background-color: var(--primary-400);
  transform: scale(1.2);
  box-shadow: 0 0 8px 0 var(--primary-500);
}

/* 動畫效果 */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

@keyframes pulse {
  from {
    opacity: 0.6;
  }

  to {
    opacity: 1;
  }
}

@keyframes shake {

  0%,
  100% {
    transform: translateX(0);
  }

  25% {
    transform: translateX(-5px);
  }

  50% {
    transform: translateX(5px);
  }

  75% {
    transform: translateX(-5px);
  }
}

@keyframes pulseDecoration {
  0% {
    opacity: 0.2;
  }

  100% {
    opacity: 0.4;
  }
}

.pulse-animation {
  animation: tab-pulse 0.5s ease-out;
}

@keyframes tab-pulse {
  0% {
    transform: scale(1);
  }

  50% {
    transform: scale(1.1);
  }

  100% {
    transform: scale(1);
  }
}

/* 響應式設計 */
@media (max-width: 768px) {
  .features-tabs {
    justify-content: flex-start;
    width: 100%;
    padding-bottom: 1rem;
  }

  .feature-tab {
    font-size: 0.9rem;
    padding: 0.7rem 1rem;
  }

  .game-features {
    padding: 3rem 0;
    min-height: 900px;
  }

  .section-title {
    font-size: 2rem;
    margin-bottom: 2rem;
  }

  .feature-content-wrapper {
    padding: 1rem;
  }

  .decoration-item {
    width: 30px;
    height: 30px;
  }

  .feature-content-wrapper:hover .decoration-item {
    width: 40px;
    height: 40px;
  }
}

@media (min-width: 769px) {
  .feature-content-wrapper {
    display: flex;
    padding: 2rem;
  }

  .game-features {
    min-height: 700px;
  }
}
</style>
