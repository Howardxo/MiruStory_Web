<script setup>
const props = defineProps({
  title: String,
  description: String,
  imagePath: String
});
</script>

<template>
  <div class="feature-item">
    <img :src="props.imagePath" :alt="props.title" class="feature-img" @error="(event) => {
      event.target.style.display = 'none';
      event.target.parentElement.classList.add('image-error');
    }" />
    <div class="feature-content">
      <h3>{{ props.title }}</h3>
      <p>{{ props.description }}</p>
    </div>
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