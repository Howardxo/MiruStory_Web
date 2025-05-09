<script setup>
import { ref, onMounted, reactive, onBeforeUnmount } from 'vue';

// 添加動畫控制
const isVisible = ref(false);
const highlightItems = ref([false, false, false]);
const typewriterComplete = ref(false);
const rateCount = ref([0, 0, 0]);
const rateTarget = [1, 1, 1]; // 目標倍率值
const imageLoaded = ref(false);
const showFloatingElements = ref(false);
const buttonHovered = ref(false);
const mousePosition = reactive({ x: 0, y: 0 });
const animationsActive = ref(true); // 控制動畫是否活躍

// 優化滑鼠追蹤效能
let mouseMoveTimeout;
const trackMouse = (event) => {
  if (mouseMoveTimeout) clearTimeout(mouseMoveTimeout);
  mouseMoveTimeout = setTimeout(() => {
    mousePosition.x = event.clientX;
    mousePosition.y = event.clientY;
  }, 10);
};

// 使用 requestAnimationFrame 實現更平滑的數字增長動畫
const startCountAnimation = () => {
  const startTime = performance.now();
  const duration = 2000;

  const animateCount = (currentTime) => {
    const elapsedTime = currentTime - startTime;
    if (elapsedTime < duration) {
      const progress = elapsedTime / duration;
      const easeOutProgress = 1 - Math.pow(1 - progress, 3);

      rateCount.value = rateTarget.map(target => {
        return parseFloat((target * easeOutProgress).toFixed(1));
      });

      if (animationsActive.value) {
        requestAnimationFrame(animateCount);
      }
    } else {
      rateCount.value = [...rateTarget];
    }
  };

  requestAnimationFrame(animateCount);
};

// 觀察元素進入視口
const observeElements = () => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        isVisible.value = true;
        observer.unobserve(entry.target);
      }
    });
  }, { threshold: 0.2 });

  const section = document.querySelector('.game-intention');
  if (section) {
    observer.observe(section);
  }
};

onMounted(() => {
  // 設置元素呈現的時間軸
  window.addEventListener('mousemove', trackMouse);
  observeElements();

  // 如果元素一開始就在視口中，直接觸發動畫
  setTimeout(() => {
    if (!isVisible.value) isVisible.value = true;
  }, 300);

  setTimeout(() => { imageLoaded.value = true; }, 800);
  setTimeout(() => { typewriterComplete.value = true; }, 1200);
  setTimeout(() => { showFloatingElements.value = true; }, 1500);

  // 依次顯示亮點項目
  setTimeout(() => { highlightItems.value[0] = true; }, 1800);
  setTimeout(() => { highlightItems.value[1] = true; }, 2100);
  setTimeout(() => { highlightItems.value[2] = true; }, 2400);

  // 啟動數字增長動畫
  setTimeout(startCountAnimation, 2700);
});

onBeforeUnmount(() => {
  window.removeEventListener('mousemove', trackMouse);
  animationsActive.value = false; // 停止所有進行中的動畫
  if (mouseMoveTimeout) clearTimeout(mouseMoveTimeout);
});
</script>

<template>
  <section class="section game-intention">
    <div class="parallax-bg"></div>
    <div class="container">
      <h2 class="section-title" :class="{ 'fade-in': isVisible }">關於咪嚕谷</h2>
      <div class="intention-content">
        <!-- 左側圖片區 -->
        <div class="intention-image" :class="{ 'slide-in': isVisible }">
          <div class="game-image" :class="{ 'image-loaded': imageLoaded }">
            <div class="floating-elements" :class="{ 'show': showFloatingElements }">
              <span class="floating-element maple-leaf">🍁</span>
              <span class="floating-element star-1">✨</span>
              <span class="floating-element star-2">✨</span>
              <span class="floating-element mushroom">🍄</span>
              <span class="floating-element cloud">☁️</span>
              <span class="floating-element heart">❤️</span>
            </div>
            <div class="image-overlay" :class="{ 'reveal': imageLoaded }">
              <div class="image-caption">探索咪嚕谷的奇幻世界</div>
            </div>
          </div>
        </div>

        <!-- 右側內容區 -->
        <div class="miru-container" :class="{ 'slide-in': isVisible }">
          <h1 class="miru-title typewriter" :class="{ 'typing-complete': typewriterComplete }">
            咪嚕谷 - 重溫童年，開啟全新冒險！
          </h1>

          <p class="miru-content fade-in-delay">
            歡迎來到咪嚕谷，一個精心打造的半懷舊楓之谷私服，重現最純粹的遊戲體驗，將童年記憶與現代元素完美融合。
          </p>

          <div class="miru-subtitle swoopIn">
            <i class="concept-icon pulse-icon"></i>遊戲理念
          </div>
          <p class="miru-content fade-up">
            在這片神奇大陸上，每位冒險家都能找到歸屬。我們致力打造一個無憂無慮的冒險天地，讓你暫時忘卻現實中的煩惱與壓力。
          </p>

          <div class="miru-highlight">
            <div class="highlight-item" :class="{ 'popIn': highlightItems[0] }">
              <span class="highlight-icon">✦</span> 無繁複課金，只有純粹樂趣
            </div>
            <div class="highlight-item" :class="{ 'popIn': highlightItems[1] }">
              <span class="highlight-icon">✦</span> 人人平等，自由探索，共建美好社群
            </div>
            <div class="highlight-item" :class="{ 'popIn': highlightItems[2] }">
              <span class="highlight-icon">✦</span> 結交真誠友誼，共同成長，共創回憶
            </div>
          </div>

          <div class="miru-subtitle rollIn">
            <i class="feature-icon pulse-icon"></i>遊戲特色
          </div>
          <p class="miru-content fade-up-delay">
            踏入咪嚕谷的那一刻，童年的歡笑與感動將重新點燃。這裡是你逃離現實壓力的避風港，也是你重新找回熱情與動力的源泉。我們精心打造平衡的遊戲世界：保留經典楓之谷的懷舊魅力，同時融入現代遊戲的精彩元素。
          </p>

          <div class="miru-rates flipInX">
            <div class="rates-title sparkle">★ 經典平衡倍率 ★</div>
            <div class="rates-grid">
              <div class="rate-item pulsate">
                <div class="rate-label">經驗值</div>
                <div class="rate-value count-up">{{ rateCount[0] }}x</div>
              </div>
              <div class="rate-item pulsate">
                <div class="rate-label">掉寶率</div>
                <div class="rate-value count-up">{{ rateCount[1] }}x</div>
              </div>
              <div class="rate-item pulsate">
                <div class="rate-label">楓幣獲得</div>
                <div class="rate-value count-up">{{ rateCount[2] }}x</div>
              </div>
            </div>
          </div>

          <div class="miru-action">
            <button class="join-button" @mouseenter="buttonHovered = true" @mouseleave="buttonHovered = false">
              今天就加入咪嚕谷
              <span class="button-glow" :class="{ 'active': buttonHovered }"></span>
              <span class="button-particles" v-if="buttonHovered"></span>
            </button>
            <div class="action-subtitle bounce">與我們一起編寫屬於你的冒險故事！</div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.section {
  padding: 4rem 0;
  position: relative;
  overflow: hidden;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.parallax-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--bg-light, #f5f7fa) 0%, #f0f8ff 100%);
  z-index: -1;
  opacity: 0.8;
  background-size: 200% 200%;
  transform: translateZ(0);
  animation: bg-shift 20s infinite alternate ease-in-out;
}

.game-intention {
  color: var(--text-dark, #333);
  position: relative;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1.5rem;
  z-index: 1;
}

.section-title {
  text-align: center;
  font-size: 2.2rem;
  margin-bottom: 2.5rem;
  position: relative;
  font-weight: 700;
  color: #2c3e50;
  opacity: 0;
  transform: translateY(0);
}

.section-title::after {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 4px;
  background: linear-gradient(to right, #3498db, #9b59b6);
  border-radius: 2px;
  transition: width 1.2s ease-in-out;
}

.section-title.fade-in {
  animation: fadeIn 0.8s forwards;
}

.section-title.fade-in::after {
  width: 80px;
}

.intention-content {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
  align-items: center;
  width: 100%;
}

@media (min-width: 992px) {
  .intention-content {
    flex-direction: row;
    align-items: flex-start;
    gap: 3rem;
  }
}

/* 左側圖片區域 */
.intention-image {
  flex: 1;
  width: 100%;
  max-width: 800px;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  opacity: 0;
  transform: translateY(20px);
}

.intention-image.slide-in {
  animation: slideIn 1.2s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

.floating-elements {
  position: absolute;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 10;
}

.floating-elements.show .floating-element {
  animation-play-state: running;
}

.floating-element {
  position: absolute;
  font-size: 24px;
  opacity: 0;
  animation: float 8s infinite alternate ease-in-out;
  animation-play-state: paused;
  filter: drop-shadow(0 3px 5px rgba(0, 0, 0, 0.2));
}

.maple-leaf {
  top: 15%;
  left: 10%;
  animation-delay: 0s;
  animation-duration: 7s;
}

.star-1 {
  top: 10%;
  right: 15%;
  animation-delay: 1.5s;
  font-size: 20px;
  animation-duration: 6s;
}

.star-2 {
  top: 30%;
  right: 30%;
  animation-delay: 3s;
  font-size: 18px;
  animation-duration: 8s;
}

.mushroom {
  bottom: 25%;
  right: 10%;
  animation-delay: 2s;
  animation-duration: 9s;
}

.cloud {
  top: 5%;
  left: 30%;
  animation-delay: 4s;
  font-size: 26px;
  animation-duration: 12s;
}

.heart {
  bottom: 15%;
  left: 15%;
  animation-delay: 3.5s;
  font-size: 18px;
  animation-duration: 7s;
}

.game-image {
  height: 320px;
  width: 100%;
  border-radius: 8px;
  object-fit: cover;
  background: url(../咪嚕.png);
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
  position: relative;
  transition: all 1s cubic-bezier(0.19, 1, 0.22, 1);
  filter: brightness(0.9) saturate(0.8);
  transform: scale(0.95);
}

.game-image.image-loaded {
  filter: brightness(1) saturate(1.05);
  transform: scale(1);
}

.game-image:hover {
  background-size: 105% auto;
  filter: brightness(1.05) saturate(1.1);
}

.image-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.8) 0%, rgba(0, 0, 0, 0) 100%);
  height: 30%;
  display: flex;
  align-items: flex-end;
  padding: 1rem;
  opacity: 0;
  transform: translateY(30px);
  transition: opacity 0.6s ease, transform 0.6s ease;
}

.image-overlay.reveal {
  opacity: 1;
  transform: translateY(0);
}

.image-caption {
  color: white;
  font-size: 1.1rem;
  font-weight: 600;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.6);
  transform: translateY(10px);
  opacity: 0;
  transition: all 0.4s ease 0.2s;
}

.image-overlay.reveal .image-caption {
  transform: translateY(0);
  opacity: 1;
}

/* 右側內容區域 */
.miru-container {
  flex: 1;
  width: 100%;
  max-width: 800px;
  font-family: '微軟正黑體', 'PingFang TC', sans-serif;
  line-height: 1.8;
  padding: 2rem;
  background: rgba(255, 255, 255, 0.92);
  border-radius: 16px;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.08);
  backdrop-filter: blur(10px);
  opacity: 0;
  transform: translateY(20px);
}

.miru-container.slide-in {
  animation: slideIn 1.2s cubic-bezier(0.175, 0.885, 0.32, 1.275) 0.3s forwards;
}

.miru-title {
  font-size: clamp(1.8rem, 4vw, 2.2rem);
  color: #e74c3c;
  text-align: center;
  margin-bottom: 1.8rem;
  font-weight: 700;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  letter-spacing: 2px;
  position: relative;
  overflow: hidden;
  white-space: nowrap;
}

.miru-title.typewriter::after {
  content: '|';
  position: absolute;
  right: 0;
  animation: blink 0.8s infinite;
}

.miru-title.typewriter {
  width: 0;
  animation: typing 1.8s steps(30, end) forwards;
}

.miru-title.typing-complete::after {
  content: '';
  animation: none;
}

.miru-title::before {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 3px;
  background: linear-gradient(90deg, #e74c3c, #f39c12, #e74c3c);
  border-radius: 2px;
  transition: width 0.8s ease 1.2s;
  background-size: 200% 100%;
  animation: gradient-shift 3s infinite ease-in-out;
}

.miru-title.typing-complete::before {
  width: 60%;
}

.miru-subtitle {
  font-size: 1.25rem;
  color: #3498db;
  margin: 1.5rem 0 1rem;
  font-weight: 700;
  border-left: 4px solid #3498db;
  padding-left: 0.75rem;
  display: flex;
  align-items: center;
  opacity: 0;
  transform: translateX(0);
}

.miru-subtitle.swoopIn {
  animation: swoopIn 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275) 1.5s forwards;
}

.miru-subtitle.rollIn {
  animation: rollIn 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275) 2.8s forwards;
}

.concept-icon,
.feature-icon {
  display: inline-block;
  width: 20px;
  height: 20px;
  margin-right: 8px;
  background-size: contain;
  background-repeat: no-repeat;
  position: relative;
}

.pulse-icon::before {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: rgba(52, 152, 219, 0.3);
  animation: pulseRing 2s infinite;
}

.miru-content {
  font-size: 1rem;
  margin-bottom: 1.2rem;
  text-align: justify;
  line-height: 1.7;
  color: #2c3e50;
  opacity: 0;
}

.miru-content.fade-in-delay {
  animation: fadeIn 1s ease-in-out 1.3s forwards;
}

.miru-content.fade-up {
  animation: fadeUp 0.8s ease-out 1.7s forwards;
}

.miru-content.fade-up-delay {
  animation: fadeUp 0.8s ease-out 3s forwards;
}

.miru-highlight {
  background-color: #f9f2e7;
  padding: 1.5rem;
  border-radius: 12px;
  margin: 1.5rem 0;
  border-left: 4px solid #f39c12;
  box-shadow: 0 5px 15px rgba(243, 156, 18, 0.1);
  position: relative;
  overflow: hidden;
}

.miru-highlight::before {
  content: '';
  position: absolute;
  top: -10px;
  left: -10px;
  right: -10px;
  bottom: -10px;
  background: radial-gradient(circle at 50% 50%, rgba(243, 156, 18, 0.15), transparent 70%);
  opacity: 0;
  transition: opacity 0.5s ease;
}

.miru-highlight:hover::before {
  opacity: 1;
}

.highlight-item {
  padding: 0.6rem 0;
  transition: all 0.5s ease;
  opacity: 0;
  transform: translateX(0);
  position: relative;
}

.highlight-item.popIn {
  animation: popIn 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

.highlight-icon {
  color: #f39c12;
  font-weight: bold;
  margin-right: 8px;
  display: inline-block;
  transition: all 0.3s ease;
}

.highlight-item:hover .highlight-icon {
  transform: scale(1.4) rotate(15deg);
  text-shadow: 0 0 10px rgba(243, 156, 18, 0.5);
}

.highlight-item::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 1px;
  background: #f39c12;
  transition: width 0.3s ease;
}

.highlight-item:hover::after {
  width: 100%;
}

.miru-rates {
  background: linear-gradient(145deg, #e8f4fc 0%, #d4e6f6 100%);
  padding: 1.5rem;
  border-radius: 12px;
  text-align: center;
  box-shadow: 0 8px 20px rgba(41, 128, 185, 0.1);
  margin: 1.5rem 0;
  overflow: hidden;
  position: relative;
  opacity: 0;
}

.miru-rates::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: radial-gradient(circle at 50% 0%, rgba(41, 128, 185, 0.2), transparent 70%);
  z-index: 0;
}

.miru-rates.flipInX {
  animation: flipInX 1s cubic-bezier(0.175, 0.885, 0.32, 1.275) 3.2s forwards;
}

.rates-title {
  font-weight: 700;
  color: #2980b9;
  font-size: 1.2rem;
  margin-bottom: 1rem;
  text-shadow: 0 1px 1px rgba(255, 255, 255, 0.8);
  position: relative;
  z-index: 1;
}

.rates-title.sparkle {
  animation: sparkle 2s infinite;
}

.rates-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  position: relative;
  z-index: 1;
}

.rate-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  padding: 0.8rem;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.4);
  position: relative;
  overflow: hidden;
}

.rate-item::before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background: linear-gradient(45deg, rgba(41, 128, 185, 0.1), transparent);
  transform: translateY(100%);
  transition: transform 0.3s ease;
}

.rate-item:hover::before {
  transform: translateY(0);
}

.rate-item.pulsate {
  animation: pulsateItem 2s infinite alternate ease-in-out;
}

.rate-item:hover {
  transform: translateY(-8px);
  background: rgba(255, 255, 255, 0.6);
  box-shadow: 0 5px 15px rgba(41, 128, 185, 0.15);
}

.rate-label {
  color: #34495e;
  font-size: 0.9rem;
  margin-bottom: 0.5rem;
  position: relative;
  z-index: 1;
}

.rate-value {
  font-size: 1.5rem;
  font-weight: bold;
  color: #2980b9;
  position: relative;
  z-index: 1;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.8);
}

.rate-value::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 2px;
  background: #2980b9;
  transition: width 0.3s ease;
}

.rate-item:hover .rate-value::after {
  width: 80%;
}

.count-up {
  display: inline-block;
  min-width: 2em;
}

.miru-action {
  text-align: center;
  margin: 2.5rem 0 1rem;
  position: relative;
}

.join-button {
  background: linear-gradient(45deg, #27ae60, #2ecc71);
  color: white;
  border: none;
  padding: 0.9rem 2.2rem;
  font-size: 1.1rem;
  font-weight: 600;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  box-shadow: 0 4px 15px rgba(46, 204, 113, 0.3);
  position: relative;
  overflow: hidden;
  z-index: 1;
  transform: translateZ(0);
}

.button-glow {
  position: absolute;
  top: -50%;
  left: -25%;
  width: 150%;
  height: 200%;
  background: linear-gradient(90deg,
      transparent,
      rgba(255, 255, 255, 0.3),
      transparent);
  transform: skewX(-20deg);
  transition: 0.5s;
  z-index: 0;
  opacity: 0;
}

.button-glow.active {
  animation: button-shine 1.5s infinite;
  opacity: 1;
}

.button-particles {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.button-particles::before,
.button-particles::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.6);
  transform: translate(-50%, -50%);
  animation: particle 0.8s linear infinite;
}

.button-particles::after {
  width: 6px;
  height: 6px;
  background-color: rgba(255, 255, 255, 0.4);
  animation-delay: 0.2s;
  animation-duration: 1s;
}

.join-button:hover {
  transform: translateY(-5px);
  box-shadow: 0 7px 20px rgba(46, 204, 113, 0.4),
    0 0 20px rgba(46, 204, 113, 0.2);
  background: linear-gradient(45deg, #219653, #27ae60);
}

.join-button:active {
  transform: translateY(-2px);
  box-shadow: 0 5px 10px rgba(46, 204, 113, 0.3);
}

.action-subtitle {
  font-size: 1rem;
  margin-top: 1rem;
  color: #7f8c8d;
  opacity: 0;
  animation: bounce 1s ease-in-out 3.5s forwards;
}

/* 滾動提示器 */
.scroll-indicator {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  opacity: 0;
  animation: fadeIn 1s ease-in-out 4s forwards;
}

.mouse {
  width: 30px;
  height: 50px;
  border: 2px solid #3498db;
  border-radius: 20px;
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
}

.wheel {
  width: 4px;
  height: 10px;
  background-color: #3498db;
  margin-top: 10px;
  border-radius: 2px;
  animation: scroll 2s infinite;
}

.scroll-indicator p {
  color: #3498db;
  font-size: 0.8rem;
  margin: 0;
}

/* 動畫效果 */
@keyframes fadeIn {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes slideIn {
  0% {
    opacity: 0;
    transform: translateY(40px);
  }

  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeUp {
  0% {
    opacity: 0;
    transform: translateY(30px);
  }

  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes typing {
  from {
    width: 0;
  }

  to {
    width: 100%;
  }
}

@keyframes blink {

  0%,
  100% {
    opacity: 1;
  }

  50% {
    opacity: 0;
  }
}

@keyframes swoopIn {
  0% {
    opacity: 0;
    transform: translateX(-30px);
  }

  100% {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes rollIn {
  0% {
    opacity: 0;
    transform: translateX(-30px) rotate(-90deg);
  }

  100% {
    opacity: 1;
    transform: translateX(0) rotate(0deg);
  }
}

@keyframes popIn {
  0% {
    opacity: 0;
    transform: scale(0.8);
  }

  70% {
    transform: scale(1.05);
  }

  100% {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes float {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0;
  }

  20% {
    opacity: 0.8;
  }

  50% {
    transform: translateY(-25px) rotate(10deg);
    opacity: 0.9;
  }

  80% {
    opacity: 0.8;
  }

  100% {
    transform: translateY(-5px) rotate(-5deg);
    opacity: 0;
  }
}

@keyframes flipInX {
  0% {
    opacity: 0;
    transform: perspective(400px) rotateX(-90deg);
  }

  40% {
    transform: perspective(400px) rotateX(20deg);
  }

  60% {
    transform: perspective(400px) rotateX(-10deg);
  }

  80% {
    transform: perspective(400px) rotateX(5deg);
  }

  100% {
    opacity: 1;
    transform: perspective(400px);
  }
}

@keyframes button-shine {
  0% {
    left: -100%;
    opacity: 0.8;
  }

  100% {
    left: 100%;
    opacity: 0;
  }
}

@keyframes particle {
  0% {
    transform: translate(-50%, -50%) scale(0);
    opacity: 1;
  }

  100% {
    transform: translate(-50%, -50%) translate(40px, -40px) scale(1);
    opacity: 0;
  }
}

@keyframes bounce {
  0% {
    opacity: 0;
    transform: translateY(-20px);
  }

  50% {
    transform: translateY(5px);
    opacity: 0.7;
  }

  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulsateItem {
  0% {
    transform: scale(1);
  }

  100% {
    transform: scale(1.03);
  }
}

@keyframes scroll {
  0% {
    transform: translateY(0);
    opacity: 1;
  }

  50% {
    transform: translateY(8px);
    opacity: 0.8;
  }

  100% {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes sparkle {

  0%,
  100% {
    text-shadow: 0 0 5px rgba(41, 128, 185, 0.5),
      0 0 10px rgba(41, 128, 185, 0.3);
  }

  50% {
    text-shadow: 0 0 15px rgba(41, 128, 185, 0.8),
      0 0 20px rgba(41, 128, 185, 0.5);
  }
}

@keyframes bg-shift {
  0% {
    background-position: 0% 0%;
  }

  100% {
    background-position: 100% 20%;
  }
}

@keyframes pulseRing {
  0% {
    transform: scale(0.8);
    opacity: 0.7;
  }

  50% {
    opacity: 0.3;
  }

  100% {
    transform: scale(1.5);
    opacity: 0;
  }
}

@keyframes gradient-shift {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

/* 響應式設計 */
@media (max-width: 767px) {
  .rates-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .rate-item {
    flex-direction: row;
    justify-content: space-between;
    padding: 0.8rem 1.2rem;
  }

  .rate-value {
    font-size: 1.3rem;
  }

  .section-title {
    font-size: 1.8rem;
  }

  .miru-title.typewriter {
    white-space: normal;
    animation: fadeIn 1.2s forwards;
    width: 100%;
  }

  .floating-element {
    font-size: 20px;
  }

  .cloud {
    font-size: 22px;
  }

  .game-image {
    height: 250px;
  }

  .join-button {
    padding: 0.8rem 1.8rem;
    font-size: 1rem;
  }
}
</style>
