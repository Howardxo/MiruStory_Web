<template>
  <div class="class-item">
    <div class="class-image">
      <img v-if="!imageError" :src="imagePath" :alt="title + '職業'" class="class-img" @error="handleImageError"
        @load="imageLoaded = true" />
      <div v-else class="image-placeholder">
        {{ title }} 職業
      </div>
    </div>
    <div class="class-content">
      <h3>{{ title }}</h3>
      <p v-if="description">{{ description }}</p>

      <div class="abilities">
        <div class="abilities-container">
          <ul>
            <li v-for="(ability, index) in abilities" :key="index" class="ability-item">
              <div class="ability-name">{{ ability.name }}</div>

              <template v-for="n in 12" :key="'desc'+n">
                <div class="skill-item"
                  v-if="ability['description' + n] && Array.isArray(ability['description' + n]) && ability['description' + n].length > 0">
                  <div class="skill-name">{{ ability['description' + n][0] }}</div>
                  <div v-for="(effect, eIndex) in ability['description' + n].slice(1)" :key="eIndex"
                    class="skill-effect">
                    {{ effect }}
                  </div>
                </div>
              </template>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

defineProps({
  title: String,
  description: String,
  abilities: {
    type: Array,
    default: () => [],
  },
  imagePath: String,
});

const imageError = ref(false)
const imageLoaded = ref(false)

const handleImageError = () => {
  imageError.value = true
}
</script>

<style scoped>
.class-item {
  display: flex;
  flex-direction: column;
  gap: var(--space-4, 1rem);
  animation: fadeIn 0.5s ease-out;
}

/* 圖片容器 - 解決垂直置中問題 */
.class-image {
  flex: 1;
  height: 250px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  background-color: var(--primary-50, #f8f9fa);

  /* 關鍵：確保圖片完美垂直置中 */
  display: flex;
  justify-content: center;
  align-items: center;
}

.class-img {
  max-height: 90%;
  max-width: 90%;
  width: auto;
  height: auto;
  object-fit: contain;
  border-radius: 30px;
  transition: opacity 0.3s ease;
}

.image-placeholder {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background-color: var(--primary-100, #e3f2fd);
  color: var(--primary-700, #1976d2);
  font-weight: 600;
  font-size: 1.25rem;
  text-align: center;
}

/* 內容區域 */
.class-content {
  flex: 1;
}

.class-content h3 {
  font-size: 1.75rem;
  margin-bottom: var(--space-2, 0.5rem);
  color: var(--primary-700, #1976d2);
  font-weight: 700;
}

.class-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: var(--space-3, 0.75rem);
  color: var(--neutral-700, #374151);
}

/* 技能區域 */
.abilities {
  margin-bottom: var(--space-3, 0.75rem);
}

/* 滾動容器 - 解決底部內容被遮擋問題 */
.abilities-container {
  max-height: 400px;
  overflow-y: auto;
  padding: 0 8px var(--space-4, 1rem) var(--space-2, 0.5rem);
  border-radius: 8px;
  box-sizing: border-box;
  scrollbar-width: thin;
  scrollbar-color: var(--primary-300, #90caf9) rgba(0, 0, 0, 0.05);
}

/* 確保底部有足夠空間 */
.abilities-container::after {
  content: '';
  display: block;
  height: var(--space-3, 0.75rem);
}

/* 自定義滾動條樣式 */
.abilities-container::-webkit-scrollbar {
  width: 8px;
}

.abilities-container::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.abilities-container::-webkit-scrollbar-thumb {
  background-color: var(--primary-300, #90caf9);
  border-radius: 4px;
  transition: all 0.3s ease;
}

.abilities-container::-webkit-scrollbar-thumb:hover {
  background-color: var(--primary-500, #2196f3);
}

/* 列表樣式 */
.abilities ul {
  list-style-type: none;
  padding-left: 0;
  margin: 0;
}

.ability-item {
  position: relative;
  padding-left: var(--space-3, 0.75rem);
  margin-bottom: var(--space-4, 1rem);
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  padding-bottom: var(--space-3, 0.75rem);
}

.ability-item:last-child {
  border-bottom: none;
  margin-bottom: var(--space-4, 1rem);
  padding-bottom: var(--space-4, 1rem);
}

.ability-item::before {
  content: "• ";
  position: absolute;
  left: 0;
  top: 0;
  color: var(--primary-500, #2196f3);
  font-weight: bold;
  font-size: 1.2rem;
}

.ability-name {
  font-weight: 600;
  color: var(--primary-600, #1565c0);
  margin-bottom: var(--space-2, 0.5rem);
  font-size: 1.1rem;
  line-height: 1.4;
}

.skill-item {
  margin-top: var(--space-2, 0.5rem);
  padding: var(--space-2, 0.5rem);
  background-color: rgba(0, 0, 0, 0.02);
  border-radius: 4px;
  margin-bottom: var(--space-2, 0.5rem);
  border-left: 3px solid var(--primary-200, #bbdefb);
}

.skill-item:last-child {
  margin-bottom: var(--space-1, 0.25rem);
}

.skill-name {
  font-weight: 500;
  color: var(--primary-500, #2196f3);
  margin-bottom: var(--space-1, 0.25rem);
  font-size: 0.95rem;
  line-height: 1.3;
}

.skill-effect {
  font-size: 0.9rem;
  color: var(--neutral-600, #6b7280);
  margin-top: var(--space-1, 0.25rem);
  line-height: 1.4;
  word-wrap: break-word;
  padding-left: var(--space-2, 0.5rem);
}

/* 動畫效果 */
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

/* 響應式設計 */
@media (max-width: 767px) {
  .abilities-container {
    max-height: 300px;
    padding: 0 4px var(--space-3, 0.75rem) var(--space-1, 0.25rem);
  }

  .class-image {
    height: 200px;
  }

  .class-content h3 {
    font-size: 1.5rem;
  }

  .ability-item {
    padding-left: var(--space-2, 0.5rem);
    margin-bottom: var(--space-3, 0.75rem);
  }
}

@media (min-width: 768px) {
  .class-item {
    flex-direction: row;
    align-items: flex-start;
    gap: var(--space-6, 1.5rem);
  }

  .class-image {
    height: 400px;
    width: 48%;
    flex-shrink: 0;
  }

  .class-content {
    width: 48%;
  }

  .abilities-container {
    max-height: 450px;
    padding: 0 8px var(--space-5, 1.25rem) var(--space-2, 0.5rem);
  }
}

@media (min-width: 1024px) {
  .class-image {
    height: 450px;
  }

  .abilities-container {
    max-height: 500px;
    padding: 0 8px var(--space-6, 1.5rem) var(--space-2, 0.5rem);
  }

  .class-content h3 {
    font-size: 2rem;
  }

  .class-content p {
    font-size: 1.15rem;
  }
}

/* 特殊情況：當內容很少時確保正確顯示 */
.abilities-container:has(.ability-item:only-child) {
  min-height: 100px;
}

/* 確保在所有瀏覽器中都有正確的滾動行為 */
.abilities-container {
  scroll-behavior: smooth;
  scroll-padding-bottom: var(--space-4, 1rem);
}
</style>
