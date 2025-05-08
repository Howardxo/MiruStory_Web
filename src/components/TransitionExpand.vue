<template>
  <div ref="container" class="container" :style="containerStyle">
    <transition :css="false" mode="out-in" @before-enter="beforeEnter" @enter="enter" @leave="leave"
      @after-leave="afterLeave" @enter-cancelled="cleanup" @leave-cancelled="cleanup">
      <div v-if="showContent" ref="content" class="content">
        <slot />
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, nextTick, watch, computed } from 'vue';

const props = defineProps({
  duration: {
    type: Number,
    default: 300
  },
  easing: {
    type: String,
    default: 'ease-in-out'
  },
  disabled: {
    type: Boolean,
    default: false
  }
});
const emit = defineEmits(['expand-start', 'expand-end', 'collapse-start', 'collapse-end']);

const container = ref(null);
const content = ref(null);
const showContent = ref(true); // 控制 slot 的切換
const containerHeight = ref('auto');
const isTransitioning = ref(false);

const containerStyle = computed(() => ({
  height: containerHeight.value,
  overflow: isTransitioning.value ? 'hidden' : ''
}));

function lockContainerHeight() {
  if (container.value) {
    containerHeight.value = container.value.offsetHeight + 'px';
    isTransitioning.value = true;
  }
}
function unlockContainerHeight() {
  containerHeight.value = 'auto';
  isTransitioning.value = false;
}

// Transition hooks
function beforeEnter(el) {
  el.style.opacity = '0';
  lockContainerHeight();
  emit('expand-start');
}

function enter(el, done) {
  if (props.disabled) {
    cleanup(el);
    unlockContainerHeight();
    done();
    return;
  }
  el.style.transition = `opacity ${props.duration / 1000}s ${props.easing}`;
  nextTick(() => {
    el.style.opacity = '1';
    setTimeout(() => {
      cleanup(el);
      unlockContainerHeight();
      done();
      emit('expand-end');
    }, props.duration);
  });
}

function leave(el, done) {
  if (props.disabled) {
    cleanup(el);
    unlockContainerHeight();
    done();
    return;
  }
  emit('collapse-start');
  el.style.transition = `opacity ${props.duration / 1000}s ${props.easing}`;
  el.style.opacity = '0';
  setTimeout(() => {
    cleanup(el);
    done();
  }, props.duration);
}

function afterLeave(el) {
  emit('collapse-end');
  // 這裡不立即 unlock，等 enter 完成再解鎖
}

function cleanup(el) {
  if (!el) return;
  el.style.transition = '';
  el.style.opacity = '';
}

// 監控 slot 內容切換時，先鎖定高度
watch(showContent, async (val, oldVal) => {
  if (container.value) {
    lockContainerHeight();
    await nextTick();
    // 內容切換後，容器高度會自動適應新內容，等動畫結束再解鎖
  }
});
</script>

<style scoped>
.container {
  transition: height 0.2s cubic-bezier(.4, 0, .2, 1);
  /* 你也可以根據需求調整這裡的 transition 時間和曲線 */
  will-change: height;
}

.content {
  /* slot 內容的淡入淡出動畫 */
}
</style>
