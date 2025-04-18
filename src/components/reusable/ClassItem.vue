<script setup lang="ts">
defineProps<{
  title: string;
  description: string;
  abilities: Array<{ name: string; description1?: string; description2?: string; description3?: string; description4?: string; description5?: string; description6?: string; description7?: string }>;
  imagePath: string;
}>();
</script>

<template>
  <div class="class-item">
    <div class="class-image">
      <img 
        :src="imagePath" 
        :alt="title + '職業'" 
        class="class-img"
        @error="(event) => { 
          (event.target as HTMLImageElement).style.display = 'none';
          (event.target as HTMLImageElement).parentElement!.classList.add('image-error');
        }" 
      />
      <div class="image-placeholder" :title="title"></div>
    </div>
    <div class="class-content">
      <h3>{{ title }}</h3>
      <p>{{ description }}</p>
      
      <div class="abilities">
        <ul>
          <li v-for="(ability, index) in abilities" :key="index" class="ability-item">
            <div class="ability-name">{{ ability.name }}</div>
            <div class="ability-description">{{ ability.description1 }}</div>
            <div class="ability-description">{{ ability.description2 }}</div>
            <div class="ability-description">{{ ability.description3 }}</div>
            <div class="ability-description">{{ ability.description4 }}</div>
            <div class="ability-description">{{ ability.description5 }}</div>
            <div class="ability-description">{{ ability.description6 }}</div>
            <div class="ability-description">{{ ability.description7 }}</div>
          </li>
        </ul>
      </div>
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
  position: relative;
  height: 250px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.class-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 8px;
  display: block;
}

.image-placeholder {
  display: none;
  width: 100%;
  height: 100%;
  background-color: var(--primary-100);
  border-radius: 8px;
  position: absolute;
  top: 0;
  left: 0;
  overflow: hidden;
}

.image-error .image-placeholder {
  display: block;
}

.image-placeholder::after {
  content: attr(title) " 職業";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--primary-700);
  font-weight: 600;
  font-size: 1.25rem;
}

.class-content {
  flex: 1;
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

.ability-item {
  position: relative;
  padding-left: var(--space-2);
  margin-bottom: var(--space-2);
}

.ability-item::before {
  content: "•";
  position: absolute;
  left: 0;
  color: var(--primary-500);
  font-weight: bold;
}

.ability-name {
  font-weight: 600;
  color: var(--primary-600);
}

.ability-description {
  font-size: 0.9rem;
  color: var(--neutral-600);
  margin-top: 0.2rem;
  padding-left: var(--space-1);
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

@media (min-width: 1024px) {
  .class-img {
    height: 80%;
    width: auto;
    object-fit: cover;
    object-fit: fill;
    border-radius: 8px;
    display: block;
}
  .class-image {
    height: 400px;
  }
}

@media (min-width: 768px) {
  .class-img {
    position: relative;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .class-image {
    height: 520px;
  }

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
