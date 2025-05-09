<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

const props = defineProps({
  title: String,
  description: [String, Array],
  imagePath: String
});

// 確保描述始終是陣列格式
const descriptions = computed(() => {
  if (Array.isArray(props.description)) {
    return props.description;
  }
  return [props.description].filter(Boolean);
});

// 圖片查看器狀態
const isImageViewerOpen = ref(false);

// 圖片載入狀態
const imageLoaded = ref(false);
const imageError = ref(false);

// 打開圖片查看器
const openImageViewer = () => {
  if (!imageError.value) {
    isImageViewerOpen.value = true;
    document.body.style.overflow = 'hidden';
  }
};

// 關閉圖片查看器
const closeImageViewer = () => {
  isImageViewerOpen.value = false;
  document.body.style.overflow = '';
};

// 圖片載入成功
const handleImageLoad = () => {
  imageLoaded.value = true;
};

// 圖片載入失敗
const handleImageError = () => {
  imageError.value = true;
};

// 按ESC關閉查看器
const handleKeyDown = (event) => {
  if (event.key === 'Escape' && isImageViewerOpen.value) {
    closeImageViewer();
  }
};

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown);
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown);
  document.body.style.overflow = '';
});
</script>

<template>
  <div class="feature-wrapper">
    <div class="feature-item">
      <div class="feature-img-wrapper">
        <div v-if="imageError" class="image-error-placeholder">
          圖片無法載入
        </div>
        <img v-if="!imageError" :src="props.imagePath" :alt="props.title" class="feature-img"
          :class="{ 'clickable': !imageError, 'loaded': imageLoaded }" loading="lazy" @click="openImageViewer"
          @load="handleImageLoad" @error="handleImageError" />
      </div>

      <div class="feature-content">
        <h3 class="feature-title">{{ props.title }}</h3>
        <div class="descriptions-container">
          <p v-for="(desc, index) in descriptions" :key="index" class="description-item">
            {{ desc }}
          </p>
        </div>
      </div>
    </div>

    <!-- 圖片查看器 -->
    <Teleport to="body">
      <transition name="fade">
        <div v-if="isImageViewerOpen" class="image-viewer-overlay" @click="closeImageViewer">
          <div class="image-viewer-container" @click.stop>
            <img :src="props.imagePath" :alt="props.title" class="image-viewer-img" />
            <button class="close-button" @click="closeImageViewer" aria-label="關閉圖片查看器">×</button>
            <div class="image-caption">{{ props.title }}</div>
          </div>
        </div>
      </transition>
    </Teleport>
  </div>
</template>

<style scoped>
/* 主要容器及布局 */
.feature-wrapper {
  width: 100%;
  padding: 1rem 0;
  display: flex;
  justify-content: center;
}

.feature-item {
  display: flex;
  width: 100%;
  max-width: 1200px;
  min-height: 250px;
  gap: 2rem;
  padding: 1rem;
  box-sizing: border-box;
  align-items: center;
}

/* 圖片區域 */
.feature-img-wrapper {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 200px;
  max-width: 60%;
}

.feature-img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  opacity: 0;
  transition: opacity 0.5s ease, transform 0.3s ease;
  object-fit: contain;
}

.feature-img.loaded {
  opacity: 1;
}

/* 圖片錯誤狀態 */
.image-error-placeholder {
  width: 100%;
  height: 200px;
  background-color: #f5f5f5;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
  font-size: 0.9rem;
}

/* 可點擊效果 */
.clickable {
  cursor: pointer;
}

.clickable:hover {
  transform: scale(1.03);
}

/* 內容區域 */
.feature-content {
  flex: 1;
  max-width: 40%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.feature-title {
  font-size: 1.75rem;
  margin-top: 0;
  margin-bottom: 1rem;
  color: #4a6cf7;
  line-height: 1.3;
}

.descriptions-container {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.description-item {
  font-size: 1.1rem;
  line-height: 1.6;
  color: #CBD5E1;
  margin: 0;
}

/* 圖片查看器 */
.image-viewer-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.9);
  backdrop-filter: blur(3px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.image-viewer-container {
  position: relative;
  max-width: 90%;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.image-viewer-img {
  max-width: 100%;
  max-height: 80vh;
  object-fit: contain;
  border-radius: 4px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
}

.close-button {
  position: absolute;
  top: -40px;
  right: 0;
  background: none;
  border: none;
  color: white;
  font-size: 2rem;
  cursor: pointer;
  padding: 5px;
  transition: transform 0.3s ease;
  opacity: 0.8;
  z-index: 1010;
}

.close-button:hover {
  transform: scale(1.2);
  opacity: 1;
}

.image-caption {
  color: white;
  text-align: center;
  padding: 1rem 0;
  font-size: 1.2rem;
  width: 100%;
}

/* 動畫 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 響應式設計 */
/* 大型螢幕 */
@media (min-width: 1400px) {
  .feature-img-wrapper {
    max-width: 50%;
  }

  .feature-content {
    max-width: 50%;
  }
}

/* 中型螢幕 */
@media (max-width: 1200px) {
  .feature-item {
    gap: 1.5rem;
  }

  .feature-title {
    font-size: 1.5rem;
  }

  .description-item {
    font-size: 1rem;
  }
}

/* 平板 */
@media (max-width: 992px) {
  .feature-item {
    gap: 1rem;
  }

  .feature-img-wrapper {
    max-width: 55%;
  }

  .feature-content {
    max-width: 45%;
  }
}

/* 平板豎屏和大型手機 */
@media (max-width: 768px) {
  .feature-item {
    flex-direction: column;
    align-items: center;
    gap: 1.5rem;
  }

  .feature-img-wrapper {
    max-width: 80%;
    justify-content: center;
    order: 1;
  }

  .feature-content {
    max-width: 100%;
    align-items: center;
    text-align: left;
    order: 2;
  }
}

/* 中小型手機 */
@media (max-width: 576px) {
  .feature-wrapper {
    padding: 0.5rem 0;
  }

  .feature-item {
    padding: 0.5rem;
    gap: 1rem;
  }

  .feature-img-wrapper {
    max-width: 90%;
  }

  .feature-title {
    font-size: 1.35rem;
    margin-bottom: 0.75rem;
  }

  .description-item {
    font-size: 0.95rem;
  }

  .close-button {
    top: -35px;
    font-size: 1.5rem;
  }

  .image-caption {
    font-size: 1rem;
    padding: 0.75rem 0;
  }
}

/* 小型手機 */
@media (max-width: 400px) {
  .feature-img-wrapper {
    max-width: 100%;
  }

  .feature-title {
    font-size: 1.25rem;
  }
}
</style>
