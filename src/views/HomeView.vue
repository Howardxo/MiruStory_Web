<script setup lang="ts">
import TheHeader from '../components/TheHeader.vue';
import GameIntention from '../components/GameIntention.vue';
import GameFeatures from '../components/GameFeatures.vue';
import SkillAdjustment from '../components/SkillAdjustment.vue';
import TheFooter from '../components/TheFooter.vue';
</script>

<template>
  <div class="home">
    <TheHeader />
    <main>
      <GameIntention />
      <GameFeatures class="game-features" />
      <SkillAdjustment class="skill-adjustment" />
    </main>
    <TheFooter />
  </div>
</template>

<style scoped>
.home {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1;
}

@media (min-width: 768px) {
  .game-features {
    height: 800px;
  }
}

</style>