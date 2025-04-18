<script setup lang="ts">
defineProps<{
  title: string;
  description: string;
  imagePath: string;
}>();
</script>

<template>
  <div class="feature-item">
    <div class="feature-image">
      <div class="image-placeholder" :title="title"></div>
    </div>
    <div class="feature-content">
      <h3>{{ title }}</h3>
      <p>{{ description }}</p>
      <button class="learn-more-btn">Learn More</button>
    </div>
  </div>
</template>

<style scoped>
.feature-item {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  animation: fadeIn 0.5s ease-out;
}

.feature-image {
  flex: 1;
}

.feature-content {
  flex: 1;
}

.image-placeholder {
  width: 100%;
  height: 250px;
  background-color: var(--primary-800);
  border-radius: 8px;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.image-placeholder::after {
  content: attr(title);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--primary-200);
  font-weight: 600;
  font-size: 1.25rem;
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
}

.learn-more-btn {
  background-color: var(--primary-600);
  color: white;
  border: none;
  padding: var(--space-1) var(--space-3);
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
}

.learn-more-btn:hover {
  background-color: var(--primary-500);
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
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

@media (min-width: 768px) {
  .feature-item {
    flex-direction: row;
    align-items: center;
  }
  
  .feature-image, 
  .feature-content {
    width: 48%;
  }
}
</style>