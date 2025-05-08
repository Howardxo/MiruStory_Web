<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const props = defineProps({
  title: String,
  description: String,
  imagePath: String
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
      <p>{{ props.description }}</p>
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
  gap: var(--space-20);
  animation: fadeIn 0.5s ease-out;
}

.feature-img {
  width: 70%;
  border-radius: 8px;
  margin-top: 28px;
}

/* 讓圖片看起來可點擊 */
.clickable {
  cursor: pointer;
  transition: transform 0.3s;
}

.clickable:hover {
  transform: scale(1.03);
}

.feature-content {
  flex: 1;
}

.feature-content h3 {
  font-size: 1.75rem;
  margin-bottom: var(--space-2);
  color: var(--primary-300);
}

.feature-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: var(--space-3);
  color: var(--neutral-300);
  width: 300px;
}

/* 以下為圖片查看器樣式 */
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
  animation: fadeIn 0.3s;
}

.image-viewer-container {
  position: relative;
  max-width: 90%;
  max-height: 90%;
  animation: scaleIn 0.3s;
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
  transition: transform 0.3s;
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

/* 保留您原有的媒體查詢和其他樣式 */
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

@media (min-width: 768px) {
  .feature-img {
    width: 60%;
  }

  .feature-item {
    gap: var(--space-8);
  }
}

@media (max-width: 768px) {
  .feature-item {
    flex-direction: column;
    align-items: center;
    gap: var(--space-8);
  }

  .feature-img {
    width: 100%;
  }
}

@media (max-width: 320px) {
  .feature-img {
    width: 100%;
  }
}
</style>
