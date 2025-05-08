<script setup lang="ts">
// No props or state needed for this component
</script>

<template>
  <section class="section game-intention">
    <div class="container">
      <h2 class="section-title">關於咪嚕谷</h2>
      <div class="intention-content">
        <div class="intention-image">
          <div class="game-image" />
          <div class="image-placeholder"></div>
        </div>
        <div class="intention-text">
          <p>
            遊玩系統還在完善中!
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

.game-image {
  height: 320px;
  border-radius: 8px;
  object-fit: cover;
  display: flex;
  justify-content: center;
  align-items: center;
  background: url(../咪嚕.png);
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
}

.intention-text {
  flex: 1;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  margin: auto;
}

.intention-text p {
  font-size: 1.1rem;
  margin-bottom: var(--space-2);
  line-height: 1.6;
}

.image-placeholder::after {
  content: attr(title);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: var(--primary-700);
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