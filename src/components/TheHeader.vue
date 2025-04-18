<script setup lang="ts">
// No props or state needed for this component
</script>

<template>
  <header class="header">
    <div class="header-content container">
      <div class="logo">MiruStory</div>
      <div class="header-text">
        <h1>加入我們一起來當冒險者吧!</h1>
        <p>世界需要您的協助</p>
        <button class="download-btn">下載遊戲</button>
      </div>
    </div>
  </header>
</template>

<style scoped>
.header {
  height: 100vh;
  min-height: 600px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  color: white;
  background: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.7)), url('https://drive.google.com/thumbnail?id=1Xg3IKMPFENCmDQkYz3E2TBRbwQ1I41L5&sz=w1200');
  background-size: cover;
  background-position: center;
}

.header-content {
  z-index: 1;
  max-width: 800px;
}

.logo {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: var(--space-6);
  letter-spacing: 1px;
  text-transform: uppercase;
  color: white;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
  animation: fadeInDown 1s ease-out;
}

.header-text {
  animation: fadeIn 1.5s ease-out;
}

.header-text h1 {
  font-size: 3rem;
  margin-bottom: var(--space-2);
  font-weight: 800;
  letter-spacing: -0.5px;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.header-text p {
  font-size: 1.25rem;
  max-width: 600px;
  margin: 0 auto var(--space-4);
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

.download-btn {
  background-color: var(--primary-500);
  color: white;
  border: none;
  padding: var(--space-2) var(--space-4);
  font-size: 1.1rem;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.download-btn:hover {
  background-color: var(--primary-600);
  transform: translateY(-2px);
  box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15);
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 768px) {
  .header-text h1 {
    font-size: 2.2rem;
  }
  
  .header-text p {
    font-size: 1rem;
  }
  
  .logo {
    font-size: 2rem;
  }
}
</style>