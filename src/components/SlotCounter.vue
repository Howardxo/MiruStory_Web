<template>
    <div class="slot-counter-container">
        <div class="slot-counter-wrapper">
            <div class="slot-counter" :style="{ transform: `translateY(${currentOffset}px)` }"
                :class="{ 'animating': isAnimating }">
                <div v-for="(item, index) in slotItems" :key="index" class="slot-item">{{ item }}</div>
            </div>
        </div>
        <span class="counter-suffix">{{ suffix }}</span>
    </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';

const props = defineProps({
    value: {
        type: Number,
        required: true
    },
    suffix: {
        type: String,
        default: ''
    },
    duration: {
        type: Number,
        default: 2
    },
    delay: {
        type: Number,
        default: 0
    }
});

// 創建拉霸項目，從0到30的數字
const slotItems = computed(() => {
    const nums = [];
    // 生成隨機數字供拉霸顯示
    for (let i = 0; i < 20; i++) {
        nums.push(Math.floor(Math.random() * 10));
    }
    // 最後附加實際值
    nums.push(props.value);
    return nums;
});

const itemHeight = ref(60); // 每個數字項目的高度
const currentOffset = ref(0);
const isAnimating = ref(false);

// 計算最終偏移量
const finalOffset = computed(() => {
    return -(slotItems.value.length - 1) * itemHeight.value;
});

onMounted(() => {
    // 延遲開始動畫
    setTimeout(() => {
        startAnimation();
    }, props.delay * 1000);
});

// 啟動拉霸動畫
function startAnimation() {
    isAnimating.value = true;

    // 使用CSS動畫
    currentOffset.value = finalOffset.value;

    // 動畫結束後
    setTimeout(() => {
        isAnimating.value = false;
    }, props.duration * 1000);
}

watch(() => props.value, (newValue) => {
    // 值變化時重新啟動動畫
    currentOffset.value = 0;
    setTimeout(() => {
        startAnimation();
    }, 100);
});
</script>

<style scoped>
.slot-counter-container {
    display: inline-flex;
    align-items: center;
    position: relative;
    overflow: hidden;
}

.slot-counter-wrapper {
    height: 60px;
    overflow: hidden;
    position: relative;
    border-radius: 4px;
    box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.15);
    background: rgba(255, 255, 255, 0.1);
}

.slot-counter {
    display: flex;
    flex-direction: column;
    transition: transform 0.2s ease;
}

.slot-counter.animating {
    transition: transform cubic-bezier(0.23, 1, 0.32, 1) var(--duration);
}

.slot-item {
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: bold;
    font-size: 1.8rem;
    min-width: 40px;
}

.counter-suffix {
    margin-left: 5px;
    font-weight: bold;
    font-size: 1.4rem;
}
</style>