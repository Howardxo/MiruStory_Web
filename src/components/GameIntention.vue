<script setup lang="ts">
// No props or state needed for this component
</script>

<template>
  <section class="section game-intention">
    <div class="container">
      <h2 class="section-title">Original Intention of the Game</h2>
      <div class="intention-content">
        <div class="intention-image">
          <div class="image-placeholder"></div>
        </div>
        <div class="intention-text">
          <p>
            MiruStory was born from a vision to create a magical world where players can forge their own destiny through meaningful choices and epic adventures.
          </p>
          <p>
            Our game combines the nostalgic elements of classic RPGs with modern gameplay mechanics, creating an experience that feels both familiar and fresh.
          </p>
          <p>
            We wanted to build a community-focused game where players can connect, collaborate, and compete in a vibrant, ever-evolving world filled with mysteries to uncover and challenges to overcome.
          </p>
          <p>
            Above all, MiruStory aims to deliver memorable moments that will stay with players long after they've logged off, creating stories worth sharing and experiences worth treasuring.
          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.game-intention {
  background-color: var(--bg-light);
  color: var(--text-dark);
}

.intention-content {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  align-items: center;
}

.intention-image {
  flex: 1;
  width: 100%;
}

.intention-text {
  flex: 1;
  width: 100%;
}

.intention-text p {
  font-size: 1.1rem;
  margin-bottom: var(--space-2);
  line-height: 1.6;
}

.image-placeholder {
  width: 100%;
  height: 300px;
  background-color: var(--primary-200);
  border-radius: 12px;
  margin-bottom: var(--space-2);
  position: relative;
  overflow: hidden;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
}

.image-placeholder::after {
  content: "Game Concept Art";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--primary-800);
  font-weight: 600;
  font-size: 1.25rem;
}

@media (min-width: 768px) {
  .intention-content {
    flex-direction: row;
    align-items: flex-start;
  }
  
  .intention-image, 
  .intention-text {
    max-width: 48%;
  }
}
</style>