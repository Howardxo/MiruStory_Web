<script setup lang="ts">
import { ref } from 'vue';
import ClassItem from './reusable/ClassItem.vue';

// Class data
const classes = [
  {
    id: 'warrior',
    title: 'Warrior',
    description: 'Masters of close combat, Warriors excel in wielding heavy weapons and armor. With high health and defense stats, they can withstand massive damage while dealing devastating blows to enemies.',
    abilities: ['Whirlwind Strike', 'Battle Cry', 'Devastate', 'Shield Wall'],
    imagePath: '/classes/warrior.jpg'
  },
  {
    id: 'mage',
    title: 'Mage',
    description: 'Wielders of arcane power, Mages control the elements and harness magical energy to decimate enemies from a distance. Though physically fragile, their spell damage and area-of-effect abilities are unmatched.',
    abilities: ['Fireball', 'Frost Nova', 'Arcane Missiles', 'Teleport'],
    imagePath: '/classes/mage.jpg'
  },
  {
    id: 'archer',
    title: 'Archer',
    description: 'Precision and agility define the Archer class. Masters of ranged combat, they strike enemies with deadly accuracy while maintaining a safe distance. Their critical hit chance and evasion skills make them formidable opponents.',
    abilities: ['Piercing Shot', 'Multishot', 'Viper Sting', 'Evasive Roll'],
    imagePath: '/classes/archer.jpg'
  },
  {
    id: 'thief',
    title: 'Thief',
    description: 'Operating from the shadows, Thieves excel in stealth and subterfuge. Their high agility and dexterity allow them to strike critical weak points, inflicting massive damage through backstabs and poison.',
    abilities: ['Backstab', 'Vanish', 'Poison Blade', 'Pickpocket'],
    imagePath: '/classes/thief.jpg'
  },
  {
    id: 'pirate',
    title: 'Pirate',
    description: 'Masters of naval combat and treasure hunting, Pirates combine ranged and melee abilities with unique luck-based skills. Their charisma can turn the tide of battle through ally buffs and enemy debuffs.',
    abilities: ['Cannonball Barrage', 'Treasure Hunter', 'Parley', 'Drunken Fury'],
    imagePath: '/classes/pirate.jpg'
  },
  {
    id: 'crazy-warrior',
    title: 'Crazy Warrior',
    description: 'Embracing battle frenzy and chaos, Crazy Warriors sacrifice defense for overwhelming offensive power. As they take damage, their attack speed and damage increase to devastating levels, making them unpredictable berserkers.',
    abilities: ['Berserk', 'Blood Frenzy', 'Reckless Assault', 'Pain Threshold'],
    imagePath: '/classes/crazy-warrior.jpg'
  }
];

const activeClass = ref(classes[0]);

const setActiveClass = (characterClass: typeof classes[0]) => {
  activeClass.value = characterClass;
};
</script>

<template>
  <section class="section skill-adjustment">
    <div class="container">
      <h2 class="section-title">Skill Adjustment</h2>
      
      <div class="class-tabs">
        <button 
          v-for="characterClass in classes" 
          :key="characterClass.id"
          class="class-tab" 
          :class="{ active: activeClass.id === characterClass.id }"
          @click="setActiveClass(characterClass)"
        >
          {{ characterClass.title }}
        </button>
      </div>
      
      <ClassItem 
        :title="activeClass.title"
        :description="activeClass.description"
        :abilities="activeClass.abilities"
        :image-path="activeClass.imagePath"
      />
    </div>
  </section>
</template>

<style scoped>
.skill-adjustment {
  background-color: var(--bg-light);
  color: var(--text-dark);
  min-height: 600px;
}

.class-tabs {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: var(--space-2);
  margin-bottom: var(--space-4);
}

.class-tab {
  background-color: var(--neutral-100);
  color: var(--neutral-700);
  border: 1px solid var(--neutral-300);
  padding: var(--space-1) var(--space-2);
  border-radius: 8px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.class-tab:hover {
  background-color: var(--primary-100);
  color: var(--primary-700);
  border-color: var(--primary-200);
}

.class-tab.active {
  background-color: var(--primary-500);
  color: white;
  border-color: var(--primary-600);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

@media (max-width: 768px) {
  .class-tabs {
    flex-direction: column;
    align-items: center;
  }
  
  .class-tab {
    width: 100%;
    max-width: 300px;
    text-align: center;
  }
}
</style>