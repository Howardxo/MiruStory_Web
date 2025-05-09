<script setup>
import { ref, onMounted, computed, nextTick, watch } from 'vue';
import ClassItem from '../components/reusable/ClassItem.vue';
import TransitionExpand from '../components/TransitionExpand.vue';
import jobData from '../data/job.json';

// 使用ref建立響應式數據
const classes = ref([]);
const activeClassIndex = ref(0);
const previousIndex = ref(0);
const loading = ref(true);
const error = ref(null);

// 動畫控制
const isVisible = ref(false);
const tabsAnimated = ref(false);
const tabIndicatorPos = ref({ left: 0, width: 0 });
const classRefs = ref([]);
const sectionRef = ref(null);
const readyForAnimation = ref(false);

// 計算當前選中的職業
const activeClass = computed(() =>
  activeClassIndex.value !== null ? classes.value[activeClassIndex.value] : null
);

// 設置當前選中的職業並更新指示器
const setActiveClass = async (index) => {
  if (index === activeClassIndex.value) return;

  previousIndex.value = activeClassIndex.value;
  activeClassIndex.value = index;

  // 添加切換動畫效果
  await nextTick();
  updateTabIndicator(index);

  // 為當前選中標籤添加特殊動畫
  if (classRefs.value && classRefs.value[index]) {
    classRefs.value.forEach(tab => {
      tab.classList.remove('glow-animation');
    });
    classRefs.value[index].classList.add('glow-animation');
  }
};

// 更新標籤指示器位置
const updateTabIndicator = (index) => {
  if (!classRefs.value || !classRefs.value[index]) return;

  const tab = classRefs.value[index];
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
        setTimeout(() => { readyForAnimation.value = true; }, 600);
        observer.disconnect();
      }
    });
  }, { threshold: 0.1 });

  if (sectionRef.value) {
    observer.observe(sectionRef.value);
  }
};

// 初始化職業標籤效果和自動循環功能
const initClassTabsEffects = () => {
  // 初始化標籤指示器位置
  nextTick(() => {
    updateTabIndicator(activeClassIndex.value);

    // 創建自動循環展示（可選）
    let autoChangeInterval;
    const startAutoChange = () => {
      autoChangeInterval = setInterval(() => {
        const nextIndex = (activeClassIndex.value + 1) % classes.value.length;
        setActiveClass(nextIndex);
      }, 10000); // 每10秒切換一次
    };

    const stopAutoChange = () => {
      clearInterval(autoChangeInterval);
    };

    // 啟動自動循環
    startAutoChange();

    // 用戶互動時暫停自動循環
    const container = document.querySelector('.skill-adjustment');
    if (container) {
      container.addEventListener('mouseenter', stopAutoChange);
      container.addEventListener('touchstart', stopAutoChange, { passive: true });
      container.addEventListener('mouseleave', startAutoChange);
      container.addEventListener('touchend', startAutoChange, { passive: true });
    }
  });
};

// 組件掛載時設置數據
onMounted(async () => {
  try {
    // 模擬數據加載延遲
    await new Promise(r => setTimeout(r, 300));

    classes.value = jobData;
    if (jobData.length > 0) {
      activeClassIndex.value = 0;
      previousIndex.value = 0;
    }
    loading.value = false;

    // 設定元素引用後進行初始化
    await nextTick();
    observeIntersection();
    initClassTabsEffects();

  } catch (e) {
    error.value = e?.message || "未知錯誤";
    loading.value = false;
    console.error('獲取職業數據時出錯:', e);
  }
});
</script>

<template>
  <section ref="sectionRef" class="section skill-adjustment" :class="{ 'is-visible': isVisible }">
    <div class="skill-bg-pattern"></div>

    <div class="container">
      <h2 class="section-title" :class="{ 'slide-in': isVisible }">
        <span class="title-line left"></span>
        技能調整
        <span class="title-line right"></span>
      </h2>

      <div v-if="loading" class="loading">
        <div class="loading-spinner"></div>
        <div class="loading-text">正在載入職業數據...</div>
      </div>

      <div v-else-if="error" class="error">
        <i class="error-icon">⚠️</i>
        <div>加載數據時出錯: {{ error }}</div>
      </div>

      <div v-else class="skill-content" :class="{ 'animated': readyForAnimation }">
        <div class="tabs-wrapper" :class="{ 'animated': tabsAnimated }">
          <div class="class-tabs">
            <button v-for="(characterClass, index) in classes" :key="characterClass.id" class="class-tab"
              :class="{ 'active': activeClassIndex === index }" @click="setActiveClass(index)"
              ref="el => { if (el) classRefs[index] = el }">
              <span class="tab-icon" v-if="characterClass.icon">{{ characterClass.icon }}</span>
              <span class="tab-text">{{ characterClass.title }}</span>
              <span class="tab-glint"></span>
            </button>

            <!-- 活動標籤指示器 -->
            <div class="tab-indicator" :style="{
              left: `${tabIndicatorPos.left}px`,
              width: `${tabIndicatorPos.width}px`
            }"></div>
          </div>
        </div>

        <TransitionExpand :active-index="activeClassIndex">
          <div v-if="activeClass" :key="activeClass.id" class="class-content-wrapper">
            <ClassItem class="class-item" :title="activeClass.title" :abilities="activeClass.abilities"
              :description="activeClass.description || ''" :image-path="activeClass.imagePath" />

            <!-- 裝飾元素 -->
            <div class="skill-decorations">
              <div class="skill-particle particle-1"></div>
              <div class="skill-particle particle-2"></div>
              <div class="skill-particle particle-3"></div>
              <div class="skill-particle particle-4"></div>
            </div>
          </div>
        </TransitionExpand>

        <!-- 導航點 -->
        <div class="navigation-dots">
          <button v-for="(_, index) in classes" :key="index" class="dot"
            :class="{ 'active': activeClassIndex === index }" @click="setActiveClass(index)"></button>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.skill-adjustment {
  background-color: var(--bg-light);
  color: var(--text-dark);
  min-height: 600px;
  position: relative;
  overflow: hidden;
  padding: 4rem 0;
  transition: all 0.3s ease;
}

.skill-bg-pattern {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: radial-gradient(circle at 10% 20%, rgba(0, 0, 0, 0.02) 0%, transparent 70%),
    radial-gradient(circle at 90% 80%, rgba(0, 0, 0, 0.02) 0%, transparent 70%);
  opacity: 0;
  transition: opacity 0.8s ease-out;
  z-index: 0;
}

.is-visible .skill-bg-pattern {
  opacity: 1;
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
  text-align: center;
  font-size: 2.2rem;
  margin-bottom: 3rem;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s cubic-bezier(0.17, 0.67, 0.83, 0.67);
  color: var(--text-dark);
}

.section-title.slide-in {
  opacity: 1;
  transform: translateY(0);
}

.title-line {
  height: 3px;
  width: 0;
  background: linear-gradient(90deg, transparent, var(--primary-500), transparent);
  transition: width 0.8s ease-out 0.3s;
}

.section-title.slide-in .title-line {
  width: 60px;
}

.title-line.left {
  transform-origin: right;
}

.title-line.right {
  transform-origin: left;
}

/* 加載狀態 */
.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 300px;
  padding: 3rem;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 4px solid rgba(0, 0, 0, 0.05);
  border-radius: 50%;
  border-top-color: var(--primary-500);
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

.loading-text {
  color: var(--text-dark);
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
  background: rgba(255, 0, 0, 0.05);
  border-radius: 8px;
  border: 1px solid var(--error-300);
}

.error-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  animation: shake 0.5s ease-in-out;
}

/* 技能內容樣式 */
.skill-content {
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.5s ease-out;
}

.skill-content.animated {
  opacity: 1;
  transform: translateY(0);
}

.tabs-wrapper {
  position: relative;
  margin-bottom: 2rem;
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.5s ease-out;
}

.tabs-wrapper.animated {
  opacity: 1;
  transform: translateY(0);
}

.class-tabs {
  display: flex;
  justify-content: flex-start;
  gap: 0.75rem;
  margin-bottom: 1.5rem;
  overflow-x: auto;
  white-space: nowrap;
  flex-wrap: nowrap;
  -webkit-overflow-scrolling: touch;
  padding: 0.5rem;
  position: relative;

  /* 自定義滾動條 */
  scrollbar-width: thin;
  scrollbar-color: var(--primary-300) transparent;
}

/* Webkit 滾動條樣式 */
.class-tabs::-webkit-scrollbar {
  height: 5px;
  background: rgba(0, 0, 0, 0.03);
  border-radius: 5px;
}

.class-tabs::-webkit-scrollbar-thumb {
  background-color: var(--primary-300);
  border-radius: 5px;
  transition: all 0.3s;
}

.class-tabs::-webkit-scrollbar-thumb:hover {
  background-color: var(--primary-500);
}

.tab-indicator {
  position: absolute;
  bottom: 0;
  height: 3px;
  background: linear-gradient(90deg, var(--primary-400), var(--primary-600));
  border-radius: 3px;
  transition: left 0.3s ease, width 0.3s ease;
  box-shadow: 0 0 8px 0 var(--primary-300);
}

.class-tab {
  position: relative;
  flex: 0 0 auto;
  background-color: var(--neutral-100);
  color: var(--neutral-700);
  border: 1px solid var(--neutral-300);
  border-radius: 8px;
  padding: 0.75rem 1.25rem;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  overflow: hidden;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.class-tab:hover {
  background-color: var(--primary-50);
  color: var(--primary-700);
  border-color: var(--primary-200);
  transform: translateY(-2px);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.class-tab.active {
  background-color: var(--primary-500);
  color: white;
  border-color: var(--primary-600);
  font-weight: 600;
  box-shadow: 0 4px 12px rgba(var(--primary-rgb), 0.2);
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

.tab-glint {
  position: absolute;
  top: -50%;
  left: -100%;
  width: 50px;
  height: 200%;
  background: linear-gradient(90deg,
      transparent,
      rgba(255, 255, 255, 0.3),
      transparent);
  transform: rotate(30deg);
  transition: 0.5s;
  z-index: 1;
}

.class-tab:hover .tab-glint {
  left: 200%;
  transition: 0.8s;
}

/* 技能內容樣式 */
.class-content-wrapper {
  position: relative;
  padding: 1.5rem;
  border-radius: 12px;
  background-color: white;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.06);
  overflow: hidden;
  min-height: 300px;
  transition: all 0.4s ease;
  transform: perspective(800px) rotateX(0deg);
}

.class-content-wrapper:hover {
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.1);
  transform: perspective(800px) rotateX(1deg) translateY(-5px);
}

/* 裝飾粒子 */
.skill-decorations {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}

.skill-particle {
  position: absolute;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: var(--primary-300);
  opacity: 0.3;
}

.particle-1 {
  top: 10%;
  left: 5%;
  animation: floating 8s infinite ease-in-out;
}

.particle-2 {
  top: 80%;
  left: 10%;
  animation: floating 7s infinite ease-in-out 1s;
}

.particle-3 {
  top: 20%;
  right: 7%;
  animation: floating 9s infinite ease-in-out 2s;
}

.particle-4 {
  bottom: 15%;
  right: 10%;
  animation: floating 6s infinite ease-in-out 3s;
}

/* 導航點 */
.navigation-dots {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-top: 2rem;
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: var(--neutral-300);
  border: none;
  cursor: pointer;
  padding: 0;
  transition: all 0.3s ease;
}

.dot:hover {
  background-color: var(--neutral-400);
}

.dot.active {
  background-color: var(--primary-500);
  transform: scale(1.2);
  box-shadow: 0 0 6px 0 var(--primary-300);
}

/* 動畫效果 */
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

@keyframes floating {
  0% {
    transform: translate(0, 0);
  }

  50% {
    transform: translate(5px, 10px);
  }

  100% {
    transform: translate(0, 0);
  }
}

.glow-animation {
  animation: tab-glow 0.8s ease-out;
}

@keyframes tab-glow {
  0% {
    box-shadow: 0 0 0 0 rgba(var(--primary-rgb), 0.4);
  }

  50% {
    box-shadow: 0 0 10px 3px rgba(var(--primary-rgb), 0.5);
  }

  100% {
    box-shadow: 0 0 0 0 rgba(var(--primary-rgb), 0);
  }
}

/* 響應式設計 */
@media (max-width: 768px) {
  .container {
    padding: 0 0.75rem;
  }

  .class-tabs {
    justify-content: flex-start;
    width: 100%;
    padding-bottom: 1rem;
  }

  .class-tab {
    font-size: 0.9rem;
    padding: 0.6rem 1rem;
  }

  .skill-adjustment {
    padding: 3rem 0;
  }

  .section-title {
    font-size: 1.8rem;
    margin-bottom: 2rem;
  }

  .class-content-wrapper {
    padding: 1rem;
  }

  .navigation-dots {
    margin-top: 1.5rem;
  }
}
</style>
