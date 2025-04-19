<script setup lang="ts">
import { ref } from 'vue';
import ClassItem from '../components/reusable/ClassItem.vue';

// 職業資料
const classes = [
  {
    id: 'warrior1',
    title: '英雄',
    description: '',
    abilities: [
    { name: '英雄', description1: '格檔 機率 90%  > 100%', description2: '鬥氣爆發 額外+26攻擊 > 150攻擊', description3: '無雙劍舞 傷害 260% > 600% 攻擊次數 2 次> 3次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=11ebmJovuzt6MRfZOlABZdrcCnsmqtPFE&sz=w1200'
  },
  {
    id: 'warrior2',
    title: '聖騎士',
    description: '',
    abilities: [
      { name: '聖騎士', description1: '騎士衝擊波 攻擊次數 1次 > 3次  攻擊怪物數量 1隻 > 3隻', description2: '格檔  可以提供隊友效果' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=11ebmJovuzt6MRfZOlABZdrcCnsmqtPFE&sz=w1200'
  },
  {
    id: 'warrior3',
    title: '黑騎士',
    description: '',
    abilities: [
      { name: '黑騎士', description1: '格檔 機率 70% > 100%', description2: '槍連擊 攻擊次數4次 攻擊怪物數量5隻', description3: '矛連擊 攻擊次數4次 攻擊怪物數量5隻', description4: '黑暗力量壓血血量調整 50% > 80%' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=11ebmJovuzt6MRfZOlABZdrcCnsmqtPFE&sz=w1200'
  },
  {
    id: 'mage1',
    title: '主教',
    description: '',
    abilities: [
      { name: '法師共同技能', description1: '魔利爪 攻擊傷害 40% > 110%', description2: '魔力無限 攻擊傷害+300 持續時間 40秒 > 300秒', description3: '核爆術 傷害 450% > 1200%' },
      { name: '主教', description1: '聖光 攻擊次數 1次 > 4次', description2: '復甦之光 冷卻時間 30分鐘 > 5分鐘', description3: '天使之箭 攻擊次數 1次 > 3次  攻擊傷害 240% > 500%', description4: '神聖祈禱 持續時間調至120秒 > 600秒', description5: '聖盾護鎧 等卻時間減至120秒 > 60秒', description6: '神聖之光 可以提供隊友效果', description7: '天怒 攻擊次數 1次 > 2次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1Wm4xN9aQFeR8Bp5xpEXeZusgLG-UX4oK&sz=w1200'
  },
  {
    id: 'mage2',
    title: '大魔導士(冰雷)',
    description: '',
    abilities: [
      { name: '法師共同技能', description1: '魔利爪 攻擊傷害 40% > 110%', description2: '魔力無限 攻擊傷害+300 持續時間 40秒 > 300秒', description3: '核爆術 傷害 450% > 1200%' },
      { name: '大魔導士(冰雷)', description1: '電閃雷鳴 攻擊次數 1次 > 4次', description2: '冰風暴 攻擊次數1次 > 4次', description3: '閃電連擊 攻擊傷害180% > 280% 攻擊次數 1次 > 8次', description4: '暴風雪 攻擊次數 1次 > 2次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1Wm4xN9aQFeR8Bp5xpEXeZusgLG-UX4oK&sz=w1200'
  },
  {
    id: 'mage3',
    title: '大魔導士(火毒)',
    description: '',
    abilities: [
      { name: '法師共同技能', description1: '魔利爪 攻擊傷害 40% > 110%', description2: '魔力無限 攻擊傷害+300 持續時間 40秒 > 300秒', description3: '核爆術 傷害 450% > 1200%' },
      { name: '大魔導士(火毒)', description1: '火焰箭 攻擊次數 1次 > 3次', description2: '末日烈焰 攻擊次數 1次 > 4次', description3: '劇毒麻痺 攻擊傷害 210% > 650% 攻擊次數 1次  > 3次', description4: '火流星 攻擊次數 1次 > 2次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1Wm4xN9aQFeR8Bp5xpEXeZusgLG-UX4oK&sz=w1200'
  },
  {
    id: 'archer1',
    title: '神射手',
    description: '',
    abilities: [
      { name: '神射手', description1: '升龍弩 攻擊次數 1次 > 2次', description2: '四連箭 傷害 100% > 200%' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1wXmT6Fid6uCe1LGJ1eb2-UzQGTnmpCfC&sz=w1200'
  },
  {
    id: 'archer2',
    title: '箭神',
    description: '',
    abilities: [
      { name: '箭神', description1: '箭雨 攻擊次數 1次 > 2次', description2: '暴風神射 傷害 100% > 160% 攻擊次數 1次 > 2次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1wXmT6Fid6uCe1LGJ1eb2-UzQGTnmpCfC&sz=w1200'
  },
  {
    id: 'thief1',
    title: '夜使者',
    description: '',
    abilities: [
      { name: '夜使者', description1: '無形鏢 持續時間 120秒 > 300秒', description2: '三飛閃(改) 傷害 150% > 200% 攻擊次數 3次 > 5次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=16Fla_DCn7fApULeUUd21uE0kRllcnr-f&sz=w1200'
  },
  {
    id: 'thief2',
    title: '暗影神偷',
    description: '',
    abilities: [
      { name: '暗影神偷', description1: '迴旋斬 攻擊次數 6次 > 8次 、傷害 80% > 300%', description2: '煙幕彈 冷卻時間 10分鐘 > 2分鐘', description3: '瞬步連擊 攻擊次數 2次 > 3次' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=16Fla_DCn7fApULeUUd21uE0kRllcnr-f&sz=w1200'
  },
  {
    id: 'pirate1',
    title: '槍神',
    description: '',
    abilities: [
      { name: '海盜共同技能', description1: '部分技能延遲降低' },
      { name: '槍神', description1: '海鷗特戰隊 攻擊怪物數量 6隻 > 8隻  冷卻時間 5秒 > 1秒 攻擊次數 1次 > 2次', description2: '海盜加農炮 攻擊次數 4次 > 5次' },

    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1YxMATNNkoMbya9Kzlt3cO0_DtdgsErjx&sz=w1200'
  },
  {
    id: 'pirate2',
    title: '拳霸',
    description: '',
    abilities: [
      { name: '海盜共同技能', description1: '部分技能延遲降低' },
      { name: '拳霸', description1: '增加周圍友方單位攻擊力', description2: '閃．連殺 攻擊傷害 230% > 350%', description3: '閃．爆破 攻擊傷害 400% > 700%', description4: '鬥神降世 持續時間調整為120秒 > 180秒', description5: '龍神降臨 攻擊次數 1次 > 2次' },

    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1KwtcP9KtkHFBnelfOY0PDhc_A4rj3zlF&sz=w1200'
  },
  {
    id: 'crazy-warrior',
    title: '狂狼勇士',
    description: '',
    abilities: [
      { name: '目前待技改' },
    ],
    imagePath: 'https://drive.google.com/thumbnail?id=1oegtlXo9BwGVuTzOCY1BAlvaOtXwHzd0&sz=w1200'
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
      <h2 class="section-title">技能調整</h2>
      
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
        :abilities="activeClass.abilities"
        :description="activeClass.description || ''"
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
