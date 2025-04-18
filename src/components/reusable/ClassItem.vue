<script setup lang="ts">
defineProps<{
  title: string;
  description: string;
  abilities: string[];
  imagePath: string;
}>();
</script>

<template>
  <div class="class-item">
    <div class="class-image">
      <div class="image-placeholder" :title="title"></div>
    </div>
    <div class="class-content">
      <h3>{{ title }}</h3>
      <p>{{ description }}</p>
      
      <div class="abilities">
        <h4>Special Abilities:</h4>
        <ul>
          <li v-for="(ability, index) in abilities" :key="index">
            {{ ability }}
          </li>
        </ul>
      </div>
      
      <button class="select-class-btn">Select This Class</button>
    </div>
  </div>
</template>

<style scoped>
.class-item {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  animation: fadeIn 0.5s ease-out;
}

.class-image {
  flex: 1;
}

.class-content {
  flex: 1;
}

.image-placeholder {
  width: 100%;
  height: 250px;
  background-color: var(--primary-100);
  border-radius: 8px;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.image-placeholder::after {
  content: attr(title) " Class";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--primary-700);
  font-weight: 600;
  font-size: 1.25rem;
}

.class-content h3 {
  font-size: 1.75rem;
  margin-bottom: var(--space-2);
  color: var(--primary-700);
}

.class-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: var(--space-3);
}

.abilities {
  margin-bottom: var(--space-3);
}

.abilities h4 {
  font-size: 1.1rem;
  margin-bottom: var(--space-1);
  color: var(--primary-600);
}

.abilities ul {
  list-style-type: none;
  padding-left: var(--space-2);
}

.abilities li {
  position: relative;
  padding-left: var(--space-2);
  margin-bottom: var(--space-1);
}

.abilities li::before {
  content: "•";
  position: absolute;
  left: 0;
  color: var(--primary-500);
  font-weight: bold;
}

.select-class-btn {
  background-color: var(--primary-500);
  color: white;
  border: none;
  padding: var(--space-1) var(--space-3);
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
}

.select-class-btn:hover {
  background-color: var(--primary-600);
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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
  .class-item {
    flex-direction: row;
    align-items: center;
  }
  
  .class-image, 
  .class-content {
    width: 48%;
  }
}
</style>