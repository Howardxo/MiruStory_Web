<script setup>
import { ref, onMounted, onUnmounted, reactive } from 'vue';

// 動態文字效果控制
const typedText = ref('');
const phrases = ['冒險者', '魔法師', '戰士', '弓箭手', '盜賊', '海盜'];
const currentPhrase = ref(0);
const charIndex = ref(0);
const isDeleting = ref(false);
const typingSpeed = ref(150);

// 視差效果控制
const parallaxElements = reactive({
  bg: { x: 0, y: 0 },
  logo: { x: 0, y: 0, rotate: 0 },
  title: { x: 0, y: 0 },
  particles: []
});
const mouse = reactive({ x: 0, y: 0 });
const isVisible = ref(false);
const heroLoaded = ref(false);
const particlesLoaded = ref(false);

// 交互狀態
const isHovering = ref(false);
const buttonHover = ref(false);
const magneticActive = ref(false);
const downloadButtonRef = ref(null);

// 為粒子系統生成隨機粒子
const generateParticles = () => {
  const particles = [];
  for (let i = 0; i < 30; i++) {
    particles.push({
      x: Math.random() * 100,
      y: Math.random() * 100,
      size: Math.random() * 4 + 1,
      speedX: (Math.random() - 0.5) * 0.3,
      speedY: (Math.random() - 0.5) * 0.3,
      opacity: Math.random() * 0.5 + 0.3,
      hue: Math.floor(Math.random() * 60) + 200, // 藍色到紫色範圍
      active: false
    });
  }
  parallaxElements.particles = particles;
  return particles;
};

// 更新粒子位置
const updateParticles = () => {
  parallaxElements.particles.forEach(particle => {
    // 移動粒子
    particle.x += particle.speedX;
    particle.y += particle.speedY;

    // 邊界檢查
    if (particle.x < 0) particle.x = 100;
    if (particle.x > 100) particle.x = 0;
    if (particle.y < 0) particle.y = 100;
    if (particle.y > 100) particle.y = 0;
  });

  requestAnimationFrame(updateParticles);
};

// 激活粒子效果
const activateParticle = (index) => {
  if (parallaxElements.particles[index]) {
    parallaxElements.particles[index].active = true;
    parallaxElements.particles[index].opacity = 0.9;
    setTimeout(() => {
      if (parallaxElements.particles[index]) {
        parallaxElements.particles[index].active = false;
        parallaxElements.particles[index].opacity = Math.random() * 0.5 + 0.3;
      }
    }, 1000);
  }
};

// 打字效果
const typeText = () => {
  const current = phrases[currentPhrase.value];

  if (isDeleting.value) {
    charIndex.value--;
    typingSpeed.value = 80;
  } else {
    charIndex.value++;
    typingSpeed.value = 150;
  }

  typedText.value = current.substring(0, charIndex.value);

  if (!isDeleting.value && charIndex.value === current.length) {
    isDeleting.value = true;
    typingSpeed.value = 1500;
  } else if (isDeleting.value && charIndex.value === 0) {
    isDeleting.value = false;
    currentPhrase.value = (currentPhrase.value + 1) % phrases.length;
    typingSpeed.value = 500;
  }

  setTimeout(typeText, typingSpeed.value);
};

// 視差效果
const handleMouseMove = (e) => {
  // 更新滑鼠位置
  mouse.x = e.clientX;
  mouse.y = e.clientY;

  const windowWidth = window.innerWidth;
  const windowHeight = window.innerHeight;

  // 計算中心點偏移量
  const offsetX = (mouse.x - windowWidth / 2);
  const offsetY = (mouse.y - windowHeight / 2);

  // 背景視差 - 在小螢幕上減弱效果
  const scaleFactor = windowWidth < 768 ? 0.01 : 0.02;
  parallaxElements.bg.x = offsetX * scaleFactor;
  parallaxElements.bg.y = offsetY * scaleFactor;

  // LOGO視差 - 在小螢幕上減弱效果
  const logoScaleFactor = windowWidth < 768 ? 0.015 : 0.03;
  parallaxElements.logo.x = offsetX * logoScaleFactor;
  parallaxElements.logo.y = offsetY * logoScaleFactor;
  parallaxElements.logo.rotate = (offsetX / windowWidth) * (windowWidth < 768 ? 3 : 5);

  // 標題視差 - 在小螢幕上減弱效果
  const titleScaleFactor = windowWidth < 768 ? 0.005 : 0.01;
  parallaxElements.title.x = offsetX * titleScaleFactor;
  parallaxElements.title.y = offsetY * titleScaleFactor;
};

// 磁性按鈕效果
const handleMagneticMove = (e) => {
  if (!downloadButtonRef.value || !magneticActive.value) return;

  const button = downloadButtonRef.value;
  const buttonRect = button.getBoundingClientRect();
  const buttonCenterX = buttonRect.left + buttonRect.width / 2;
  const buttonCenterY = buttonRect.top + buttonRect.height / 2;

  // 在小屏幕上減小磁性強度
  const magnetStrength = window.innerWidth < 768 ? 35 : 25;
  const distanceX = (e.clientX - buttonCenterX) / magnetStrength;
  const distanceY = (e.clientY - buttonCenterY) / magnetStrength;

  button.style.transform = `translate(${distanceX}px, ${distanceY}px) scale(${buttonHover.value ? 1.05 : 1})`;
};

// 重置磁性按鈕位置
const resetMagneticButton = () => {
  if (!downloadButtonRef.value) return;
  downloadButtonRef.value.style.transform = 'translate(0, 0) scale(1)';
};

// 啟用磁性按鈕
const enableMagneticButton = () => {
  magneticActive.value = true;
  buttonHover.value = true;
};

// 禁用磁性按鈕
const disableMagneticButton = () => {
  magneticActive.value = false;
  buttonHover.value = false;
  resetMagneticButton();
};

// 下載按鈕點擊事件
const handleDownload = (e) => {
  e.preventDefault();
  window.open('https://drive.google.com/uc?export=download&id=18SV8cqm7pzQuHN6xZqtOwSHWsjLP6LGg', '_blank');

  // 按鈕動畫效果
  if (downloadButtonRef.value) {
    downloadButtonRef.value.classList.add('animate-click');
    setTimeout(() => {
      downloadButtonRef.value?.classList.remove('animate-click');
    }, 500);
  }
};

// 液體波浪動畫控制
const wave = ref({
  amplitude: 20,
  frequency: 0.05,
  phase: 0
});

// 更新波浪動畫
const animateWave = () => {
  wave.value.phase += 0.05;
  if (wave.value.phase > Math.PI * 2) {
    wave.value.phase = 0;
  }

  requestAnimationFrame(animateWave);
};

// 初始化交叉觀察器
const initIntersectionObserver = () => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        // 如果是主要元素則標記為可見
        if (entry.target.classList.contains('header')) {
          isVisible.value = true;
        }
      }
    });
  }, { threshold: 0.1 });

  // 觀察主要元素
  document.querySelectorAll('.animate-on-scroll, .header').forEach(el => {
    observer.observe(el);
  });
};

onMounted(() => {
  // 延遲顯示各元素
  setTimeout(() => {
    isVisible.value = true;
  }, 100);

  setTimeout(() => {
    heroLoaded.value = true;
    // 生成粒子
    generateParticles();
    particlesLoaded.value = true;

    // 啟動粒子動畫
    updateParticles();

    // 啟動波浪動畫
    animateWave();
  }, 300);

  // 啟動打字效果
  setTimeout(() => {
    typeText();
  }, 1500);

  // 添加視差效果監聽
  window.addEventListener('mousemove', handleMouseMove);
  window.addEventListener('mousemove', handleMagneticMove);

  // 初始化交叉觀察器
  initIntersectionObserver();

  // 添加頁面加載類
  document.querySelector('.header')?.classList.add('loaded');
});

onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove);
  window.removeEventListener('mousemove', handleMagneticMove);
});
</script>

<template>
  <header class="header animate-on-scroll" :class="{ visible: isVisible, 'hero-loaded': heroLoaded }">
    <!-- 液態背景波浪 -->
    <div class="liquid-background">
      <svg class="wave-svg" viewBox="0 0 1440 100" preserveAspectRatio="none">
        <path class="liquid-wave" :d="`
          M0,50 
          C320,${50 + Math.sin(wave.phase) * wave.amplitude},
            720,${50 + Math.sin(wave.phase + 1) * wave.amplitude},
            1440,50 
          V100 H0 Z
        `" fill="rgba(var(--primary-rgb), 0.15)"></path>
        <path class="liquid-wave second" :d="`
          M0,50 
          C320,${50 + Math.sin(wave.phase + 0.5) * wave.amplitude * 0.8},
            720,${50 + Math.sin(wave.phase + 1.5) * wave.amplitude * 0.8},
            1440,50 
          V100 H0 Z
        `" fill="rgba(var(--primary-rgb), 0.1)"></path>
      </svg>
    </div>

    <!-- 視差背景 -->
    <div class="parallax-bg"
      :style="{ transform: `translate(${parallaxElements.bg.x}px, ${parallaxElements.bg.y}px)` }"></div>

    <!-- 交互粒子系統 -->
    <div class="interactive-particles" v-if="particlesLoaded">
      <div v-for="(particle, index) in parallaxElements.particles" :key="index" class="particle"
        :class="{ 'active': particle.active }" :style="{
          left: `${particle.x}%`,
          top: `${particle.y}%`,
          width: `${particle.size}px`,
          height: `${particle.size}px`,
          opacity: particle.opacity,
          backgroundColor: `hsla(${particle.hue}, 100%, 70%, ${particle.active ? 0.9 : 0.5})`,
          transform: particle.active ? 'scale(3)' : 'scale(1)'
        }" @mouseover="activateParticle(index)"></div>
    </div>

    <!-- 主要內容 -->
    <div class="header-content container">
      <div class="logo-container">
        <div class="logo" :style="{
          transform: `translate(${parallaxElements.logo.x}px, ${parallaxElements.logo.y}px) 
                      rotate(${parallaxElements.logo.rotate}deg)`
        }"></div>
        <div class="logo-glow"></div>
        <div class="logo-orbits">
          <div class="orbit orbit-1"></div>
          <div class="orbit orbit-2"></div>
        </div>
      </div>

      <div class="header-text" :style="{
        transform: `translate(${parallaxElements.title.x}px, ${parallaxElements.title.y}px)`
      }">
        <h1 class="title-3d">
          <span class="text-reveal">加入我們並成為</span>
          <span class="typed-text-container">
            <span class="typed-text">{{ typedText }}</span>
            <span class="cursor">|</span>
          </span>
        </h1>

        <p class="fade-up animate-on-scroll">探索這個奇幻世界，創造屬於你的冒險故事！</p>

        <div class="button-container animate-on-scroll">
          <div class="magnetic-area" @mouseenter="enableMagneticButton" @mouseleave="disableMagneticButton">
            <button ref="downloadButtonRef" class="download-btn" @click="handleDownload">
              <span class="button-content">下載遊戲</span>
              <span class="button-glow"></span>
              <span class="button-particles"></span>
            </button>
          </div>
        </div>

        <div class="feature-tags animate-on-scroll">
          <div class="tag tag-1">經典半懷舊</div>
          <div class="tag tag-2">免費遊玩</div>
          <div class="tag tag-3">多樣職業</div>
        </div>
      </div>
    </div>

    <!-- 裝飾元素 -->
    <div class="decorative-shapes">
      <div class="shape shape-1"></div>
      <div class="shape shape-2"></div>
      <div class="shape shape-3"></div>
      <div class="shape shape-4"></div>
    </div>
  </header>
</template>

<style scoped>
/* CSS變數定義 */
:root {
  --primary-rgb: 61, 90, 254;
  /* 藍色基調 */
  --accent-rgb: 255, 88, 88;
  /* 紅色強調 */
  --header-height: 100vh;
  --animation-duration: 0.8s;
  --bezier-bounce: cubic-bezier(0.5, -0.5, 0.1, 1.5);
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 1rem;
  --space-4: 1.5rem;
  --space-5: 2rem;
  --space-6: 3rem;
}

/* 主容器樣式 */
.header {
  height: var(--header-height);
  min-height: 600px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  color: white;
  overflow: hidden;
  transition: all var(--animation-duration) ease;
  opacity: 0;
  transform: translateY(20px);
  perspective: 1000px;
}

.header.visible {
  opacity: 1;
  transform: translateY(0);
}

/* 視差背景 */
.parallax-bg {
  position: absolute;
  top: -50px;
  left: -50px;
  right: -50px;
  bottom: -50px;
  background: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.8)), url('../冒險者公會.png');
  background-size: cover;
  background-position: center;
  z-index: -2;
  transition: transform 0.2s ease-out;
  filter: brightness(0.85) contrast(1.1) saturate(1.2);
}

/* 液態背景波浪 */
.liquid-background {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 150px;
  overflow: hidden;
  z-index: -1;
}

.wave-svg {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 100%;
}

.liquid-wave {
  transition: d 0.5s ease;
  opacity: 0.5;
}

.liquid-wave.second {
  opacity: 0.3;
}

/* 交互粒子系統 */
.interactive-particles {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  z-index: 0;
  overflow: hidden;
  pointer-events: none;
  /* 在小螢幕上禁用粒子互動以提高性能 */
}

.particle {
  position: absolute;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.5);
  transition: transform 0.5s var(--bezier-bounce),
    background-color 0.5s ease,
    opacity 0.5s ease,
    box-shadow 0.5s ease;
  z-index: 1;
  box-shadow: 0 0 5px rgba(255, 255, 255, 0.3);
}

.particle.active {
  box-shadow: 0 0 15px 5px rgba(var(--primary-rgb), 0.6);
  z-index: 10;
}

/* 頭部內容 */
.header-content {
  position: relative;
  z-index: 2;
  width: 100%;
  max-width: 800px;
  padding: 0 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

/* Logo樣式 */
.logo-container {
  position: relative;
  margin: 20px auto 40px;
  width: 200px;
  height: 200px;
  transform-style: preserve-3d;
  transform: perspective(800px);
}

.logo {
  height: 100%;
  width: 100%;
  background: url(../LOGO去背.png);
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
  animation: float 6s infinite ease-in-out;
  position: relative;
  z-index: 2;
  filter: drop-shadow(0 5px 15px rgba(255, 255, 255, 0.3));
  transition: transform 0.2s ease;
  transform-style: preserve-3d;
}

.logo-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 180px;
  height: 180px;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  background: radial-gradient(circle, rgba(var(--primary-rgb), 0.4) 0%, transparent 70%);
  z-index: 1;
  animation: pulse 4s infinite alternate;
}

.logo-orbits {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  z-index: 0;
}

.orbit {
  position: absolute;
  top: 50%;
  left: 50%;
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  animation: rotate 20s linear infinite;
}

.orbit-1 {
  width: 220px;
  height: 220px;
  transform: translate(-50%, -50%);
}

.orbit-2 {
  width: 260px;
  height: 260px;
  transform: translate(-50%, -50%);
  animation-duration: 30s;
  animation-direction: reverse;
  border-color: rgba(var(--primary-rgb), 0.2);
}

@keyframes rotate {
  0% {
    transform: translate(-50%, -50%) rotate(0deg);
  }

  100% {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

@keyframes float {

  0%,
  100% {
    transform: translateY(0) rotateX(0deg) rotateY(0deg);
  }

  33% {
    transform: translateY(-15px) rotateX(5deg) rotateY(5deg);
  }

  66% {
    transform: translateY(-5px) rotateX(-3deg) rotateY(-3deg);
  }
}

@keyframes pulse {
  0% {
    opacity: 0.5;
    transform: translate(-50%, -50%) scale(1);
  }

  100% {
    opacity: 0.8;
    transform: translate(-50%, -50%) scale(1.3);
  }
}

/* 標題和文字樣式 */
.header-text {
  opacity: 0;
  animation: fadeInUp 1.2s ease-out 0.5s forwards;
  transition: transform 0.2s ease;
  width: 100%;
}

.title-3d {
  transform-style: preserve-3d;
  perspective: 500px;
  margin: 0 auto var(--space-3);
}

.text-reveal {
  display: inline-block;
  overflow: hidden;
  animation: revealText 1.5s cubic-bezier(0.77, 0, 0.175, 1) forwards;
  animation-delay: 0.8s;
  opacity: 0;
  transform-style: preserve-3d;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.typed-text-container {
  display: inline-block;
  position: relative;
  perspective: 1000px;
  transform-style: preserve-3d;
  margin: 0 5px;
}

.typed-text {
  color: rgba(var(--primary-rgb), 1);
  font-weight: 700;
  position: relative;
  display: inline-block;
  text-shadow: 0 0 10px rgba(var(--primary-rgb), 0.5);
}

.typed-text::after {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 0;
  width: 100%;
  height: 3px;
  background: linear-gradient(90deg, transparent, rgba(var(--primary-rgb), 0.8), transparent);
  transform: scaleX(0);
  transform-origin: center;
  animation: lineExpand 2s infinite alternate;
}

@keyframes lineExpand {
  0% {
    transform: scaleX(0);
    opacity: 0.3;
  }

  100% {
    transform: scaleX(1);
    opacity: 1;
  }
}

.cursor {
  display: inline-block;
  margin-left: 2px;
  animation: blink 1s infinite;
  color: rgba(var(--primary-rgb), 1);
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

.header-text h1 {
  font-size: clamp(1.8rem, 5vw, 3.5rem);
  font-weight: 800;
  letter-spacing: -0.5px;
  line-height: 1.3;
  text-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.header-text p {
  font-size: clamp(1rem, 3vw, 1.25rem);
  max-width: 600px;
  margin: 0 auto var(--space-4);
  opacity: 0;
  text-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
}

/* 按鈕樣式 */
.button-container {
  position: relative;
  display: inline-block;
  opacity: 0;
  margin-bottom: var(--space-4);
}

.magnetic-area {
  padding: 30px;
  margin: -30px;
  display: inline-block;
}

.download-btn {
  position: relative;
  background: linear-gradient(135deg, rgba(var(--primary-rgb), 0.9), rgba(var(--primary-rgb), 0.7));
  color: white;
  border: none;
  padding: 1rem 2.5rem;
  font-size: 1.2rem;
  border-radius: 50px;
  font-weight: 600;
  cursor: pointer;
  overflow: hidden;
  transition: all 0.4s ease, transform 0.2s ease;
  box-shadow: 0 6px 20px rgba(var(--primary-rgb), 0.4);
  transform-style: preserve-3d;
  transform: perspective(800px) rotateX(0) rotateY(0);
  z-index: 1;
}

.button-content {
  position: relative;
  z-index: 3;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.button-glow {
  position: absolute;
  top: -50%;
  left: -25%;
  width: 150%;
  height: 200%;
  background: linear-gradient(90deg,
      transparent,
      rgba(255, 255, 255, 0.2),
      transparent);
  transform: skewX(-20deg);
  transition: 0.5s;
  z-index: 2;
}

.download-btn:hover {
  background: linear-gradient(135deg, rgba(var(--primary-rgb), 1), rgba(var(--primary-rgb), 0.8));
  box-shadow: 0 10px 25px rgba(var(--primary-rgb), 0.6),
    0 0 15px rgba(var(--primary-rgb), 0.4);
}

.download-btn:hover .button-glow {
  animation: button-shine 1.5s infinite;
}

.download-icon {
  margin-left: 10px;
  font-size: 1.1em;
  display: inline-block;
  transition: transform 0.3s ease;
}

.download-btn:hover .download-icon {
  transform: translateY(3px);
}

.button-particles {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  overflow: hidden;
}

.download-btn:hover .button-particles::before,
.download-btn:hover .button-particles::after {
  content: '';
  position: absolute;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: white;
  animation: particle-burst 0.8s ease-out forwards;
  opacity: 0;
  z-index: -1;
}

.download-btn:hover .button-particles::before {
  top: 30%;
  left: 20%;
  animation-delay: 0.1s;
}

.download-btn:hover .button-particles::after {
  top: 70%;
  left: 80%;
  animation-delay: 0.3s;
}

.download-btn.animate-click {
  animation: button-click 0.5s forwards;
}

@keyframes button-click {
  0% {
    transform: scale(1);
  }

  50% {
    transform: scale(0.95);
  }

  100% {
    transform: scale(1);
  }
}

/* 特性標籤 */
.feature-tags {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 0;
  /* 移除底部邊距，因為已經移除了滾動提示 */
  flex-wrap: wrap;
  opacity: 0;
  width: 100%;
}

.tag {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 50px;
  padding: 0.5rem 1rem;
  font-size: 0.9rem;
  display: inline-block;
  backdrop-filter: blur(5px);
  transition: all 0.3s ease;
  transform: translateY(20px);
  opacity: 0;
}

.tag-1 {
  animation: fadeTagsUp 0.5s ease-out 2.5s forwards;
}

.tag-2 {
  animation: fadeTagsUp 0.5s ease-out 2.7s forwards;
}

.tag-3 {
  animation: fadeTagsUp 0.5s ease-out 2.9s forwards;
}

.tag:hover {
  background: rgba(var(--primary-rgb), 0.3);
  border-color: rgba(var(--primary-rgb), 0.5);
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

@keyframes fadeTagsUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 裝飾形狀 */
.decorative-shapes {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  z-index: -1;
}

.shape {
  position: absolute;
  opacity: 0.3;
  filter: blur(3px);
}

.shape-1 {
  top: 15%;
  left: 10%;
  width: 80px;
  height: 80px;
  background: radial-gradient(circle, rgba(var(--primary-rgb), 0.6) 0%, transparent 70%);
  animation: float-shapes 15s infinite ease-in-out;
}

.shape-2 {
  top: 60%;
  left: 15%;
  width: 60px;
  height: 60px;
  background: radial-gradient(circle, rgba(var(--accent-rgb), 0.6) 0%, transparent 70%);
  animation: float-shapes 12s infinite ease-in-out reverse;
  animation-delay: 3s;
}

.shape-3 {
  top: 20%;
  right: 15%;
  width: 70px;
  height: 70px;
  background: radial-gradient(circle, rgba(var(--accent-rgb), 0.6) 0%, transparent 70%);
  animation: float-shapes 18s infinite ease-in-out;
  animation-delay: 5s;
}

.shape-4 {
  bottom: 20%;
  right: 10%;
  width: 90px;
  height: 90px;
  background: radial-gradient(circle, rgba(var(--primary-rgb), 0.6) 0%, transparent 70%);
  animation: float-shapes 20s infinite ease-in-out reverse;
  animation-delay: 8s;
}

@keyframes float-shapes {

  0%,
  100% {
    transform: translate(0, 0);
  }

  50% {
    transform: translate(30px, -30px);
  }
}

/* 動畫關鍵幀 */
@keyframes revealText {
  0% {
    opacity: 0;
    transform: translateY(100%);
  }

  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes fadeInUp {
  0% {
    opacity: 0;
    transform: translateY(30px);
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

@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: scale(0.8);
  }

  50% {
    opacity: 1;
    transform: scale(1.1);
  }

  100% {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes button-shine {
  0% {
    left: -100%;
    opacity: 0.7;
  }

  100% {
    left: 100%;
    opacity: 0;
  }
}

@keyframes particle-burst {
  0% {
    transform: translate(0, 0) scale(0);
    opacity: 1;
  }

  100% {
    transform: translate(0, 0) scale(20);
    opacity: 0;
  }
}

/* 交叉觀察器動畫類 */
.animate-on-scroll {
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.8s ease;
}

.animate-on-scroll.visible {
  opacity: 1;
  transform: translateY(0);
}

/* 響應式調整 */
@media (max-width: 768px) {
  .header {
    min-height: 500px;
    /* 確保小螢幕上的最小高度適當 */
    height: 100vh;
    /* 使用視窗高度單位 */
    justify-content: center;
    /* 確保內容垂直居中 */
  }

  .header-content {
    padding: 0 15px;
    justify-content: center;
    gap: 12px;
  }

  .header-text h1 {
    font-size: 1.8rem;
    line-height: 1.3;
    flex-direction: column;
    /* 在小螢幕上垂直排列標題元素 */
  }

  .typed-text-container {
    margin: var(--space-1) 0;
    /* 在小螢幕上增加垂直間距 */
  }

  .header-text p {
    font-size: 0.95rem;
    margin-bottom: var(--space-3);
    padding: 0 5px;
  }

  .logo-container {
    width: 120px;
    /* 更小的LOGO */
    height: 120px;
    margin: 10px auto 20px;
  }

  .logo-glow {
    width: 110px;
    height: 110px;
  }

  .download-btn {
    padding: 0.75rem 1.5rem;
    font-size: 1rem;
  }

  /* 調整軌道大小以配合較小的LOGO */
  .orbit-1 {
    width: 140px;
    height: 140px;
  }

  .orbit-2 {
    width: 170px;
    height: 170px;
  }

  .feature-tags {
    margin-bottom: 0;
    /* 確保底部沒有多餘間距 */
    gap: 8px;
    /* 減小標籤間的間距 */
  }

  .tag {
    font-size: 0.8rem;
    padding: 0.35rem 0.7rem;
  }

  /* 確保粒子在移動設備上數量減少，提高性能 */
  .interactive-particles {
    pointer-events: none;
    /* 在小螢幕上禁用粒子互動以提高性能 */
  }

  /* 減小裝飾形狀尺寸 */
  .shape {
    transform: scale(0.7);
  }

  /* 減小波浪高度 */
  .liquid-background {
    height: 100px;
  }
}

/* 中等螢幕調整 */
@media (min-width: 769px) and (max-width: 1024px) {
  .logo-container {
    width: 160px;
    height: 160px;
  }

  .orbit-1 {
    width: 180px;
    height: 180px;
  }

  .orbit-2 {
    width: 220px;
    height: 220px;
  }

  .header-text h1 {
    font-size: 2.5rem;
  }
}

/* 高性能設備附加效果 */
@media (min-width: 1200px) {
  .header {
    --animation-duration: 1s;
  }

  .parallax-bg {
    filter: brightness(0.85) contrast(1.1) saturate(1.2) blur(2px);
  }

  .download-btn {
    transition: all 0.4s ease, transform 0.1s ease;
  }

  .logo {
    filter: drop-shadow(0 5px 15px rgba(255, 255, 255, 0.3)) brightness(1.1);
  }

  /* 增強交互性 */
  .interactive-particles {
    pointer-events: auto;
  }
}
</style>
