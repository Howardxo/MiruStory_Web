<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

const props = defineProps({
  title: String,
  description: [String, Array], // 接受字串或陣列
  imagePath: String
});

// 計算屬性：確保描述始終是陣列格式
const descriptions = computed(() => {
  if (Array.isArray(props.description)) {
    return props.description;
  }
  return [props.description].filter(Boolean); // 過濾掉空值
});

// 控制圖片查看器顯示狀態
const isImageViewerOpen = ref(false);

// 打開圖片查看器
const openImageViewer = () => {
  isImageViewerOpen.value = true;
  document.body.style.overflow = 'hidden'; // 防止背景滾動
};

// 關閉圖片查看器
const closeImageViewer = () => {
  isImageViewerOpen.value = false;
  document.body.style.overflow = ''; // 恢復背景滾動
};

// 按ESC鍵關閉圖片查看器
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
});
</script>

<template>
  <div class="feature-item">
    <img :src="props.imagePath" :alt="props.title" class="feature-img clickable" @click="openImageViewer" @error="(event) => {
      event.target.style.display = 'none';
      event.target.parentElement.classList.add('image-error');
    }" />
    <div class="feature-content">
      <h3>{{ props.title }}</h3>
      <p v-for="(desc, index) in descriptions" :key="index" class="description-item">
        {{ desc }}
      </p>
    </div>

    <!-- 圖片查看器模態框 -->
    <Teleport to="body">
      <div v-if="isImageViewerOpen" class="image-viewer-overlay" @click="closeImageViewer">
        <div class="image-viewer-container" @click.stop>
          <img :src="props.imagePath" :alt="props.title" class="image-viewer-img" />
          <button class="close-button" @click="closeImageViewer">×</button>
          <div class="image-caption">{{ props.title }}</div>
        </div>
      </div>
    </Teleport>
  </div>
</template>

<style scoped>
.feature-item {
  display: flex;
  gap: var(--space-20, 1.25rem);
  animation: fadeIn 0.5s ease-out;
  width: 100%;
}

.feature-img {
  width: 100%;
  max-width: 70%;
  border-radius: 8px;
  margin-top: 28px;
  object-fit: cover;
}

/* 圖片錯誤狀態樣式 */
.image-error {
  position: relative;
}

.image-error::after {
  content: '圖片無法載入';
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  top: 28px;
  left: 0;
  width: 70%;
  height: 200px;
  background-color: var(--neutral-100, #f5f5f5);
  border-radius: 8px;
  color: var(--neutral-500, #666);
  font-size: 0.9rem;
}

/* 可點擊效果 */
.clickable {
  cursor: pointer;
  transition: transform 0.3s ease;
}

.clickable:hover {
  transform: scale(1.03);
}

.feature-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.feature-content h3 {
  font-size: 1.75rem;
  margin-bottom: var(--space-2, 0.5rem);
  color: var(--primary-300, #4a6cf7);
}

.feature-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: var(--space-3, 0.75rem);
  color: var(--neutral-300, #666);
  max-width: 100%;
}

/* 描述項目樣式 */
.description-item {
  margin-bottom: var(--space-3, 0.75rem);
}

/* 圖片查看器樣式 */
.image-viewer-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.9);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
  backdrop-filter: blur(3px);
}

.image-viewer-container {
  position: relative;
  max-width: 90%;
  max-height: 90%;
  animation: scaleIn 0.3s ease;
}

.image-viewer-img {
  max-width: 100%;
  max-height: 90vh;
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
}

.close-button:hover {
  transform: scale(1.2);
}

.image-caption {
  color: white;
  text-align: center;
  padding: 10px 0;
  font-size: 1.2rem;
}

/* 動畫效果 */
@keyframes scaleIn {
  from {
    transform: scale(0.9);
    opacity: 0;
  }

  to {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 響應式設計 - 桌面與平板 */
@media (min-width: 769px) {
  .feature-img {
    max-width: 60%;
  }

  .feature-item {
    gap: var(--space-8, 2rem);
  }

  .feature-content p {
    max-width: 300px;
  }
}

/* 平板 */
@media (max-width: 992px) and (min-width: 769px) {
  .feature-content h3 {
    font-size: 1.5rem;
  }

  .feature-content p {
    font-size: 1rem;
  }
}

/* 手機 */
@media (max-width: 768px) {
  .feature-item {
    flex-direction: column;
    align-items: center;
    gap: var(--space-8, 2rem);
  }

  .feature-img {
    max-width: 100%;
    margin-top: 15px;
  }

  .feature-content {
    width: 100%;
    text-align: left;
  }

  .feature-content p {
    margin-left: auto;
    margin-right: auto;
  }

  .image-error::after {
    width: 100%;
  }
}

/* 小型手機 */
@media (max-width: 480px) {
  .feature-content h3 {
    font-size: 1.5rem;
  }

  .feature-content p {
    font-size: 1rem;
  }

  .close-button {
    top: -30px;
    font-size: 1.5rem;
  }
}
</style>
