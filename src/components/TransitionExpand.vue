<template>
  <div ref="container" class="container" :style="containerStyle">
    <transition :css="false" mode="out-in" @before-enter="beforeEnter" @enter="enter" @leave="leave"
      @after-leave="afterLeave" @enter-cancelled="cleanup" @leave-cancelled="cleanup">
      <div v-if="showContent" ref="content" class="content" :key="activeIndex">
        <slot />
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, nextTick, watch, computed } from 'vue';

const props = defineProps({
  duration: { type: Number, default: 300 },
  easing: { type: String, default: 'cubic-bezier(0.4, 0, 0.2, 1)' },
  activeIndex: { type: Number, default: 0 },
  disabled: { type: Boolean, default: false }
});
const emit = defineEmits(['expand-start', 'expand-end', 'collapse-start', 'collapse-end']);

const container = ref(null);
const content = ref(null);
const showContent = ref(true);
const containerHeight = ref('auto');
const isTransitioning = ref(false);
const slideDirection = ref('right');
const prevIndex = ref(props.activeIndex);
let animationInProgress = false;

// 監聽 activeIndex 變化，決定滑動方向
watch(() => props.activeIndex, (newIndex) => {
  slideDirection.value = newIndex > prevIndex.value ? 'right' : 'left';
  prevIndex.value = newIndex;
});

// 動態樣式
const containerStyle = computed(() => ({
  height: containerHeight.value,
  overflow: isTransitioning.value ? 'hidden' : '',
  position: 'relative'
}));

// 鎖定容器高度
function lockContainerHeight() {
  if (!container.value) return;
  const height = container.value.offsetHeight;
  containerHeight.value = `${height}px`;
  isTransitioning.value = true;
}

// 解鎖容器高度
function unlockContainerHeight() {
  requestAnimationFrame(() => {
    containerHeight.value = 'auto';
    isTransitioning.value = false;
    animationInProgress = false;
  });
}

// 計算滑動距離
function getSlideDistance() {
  if (!container.value) return 0;
  return container.value.getBoundingClientRect().width;
}

// 過渡鉤子
function beforeEnter(el) {
  if (animationInProgress) return;
  animationInProgress = true;
  lockContainerHeight();
  emit('expand-start');

  const distance = getSlideDistance();
  const initialX = slideDirection.value === 'right' ? distance : -distance;
  el.style.transform = `translateX(${initialX}px)`;
  el.style.opacity = '0';
  el.style.willChange = 'transform, opacity';
}

function enter(el, done) {
  if (props.disabled) {
    cleanup(el);
    unlockContainerHeight();
    done();
    return;
  }
  requestAnimationFrame(() => {
    el.style.transition = `transform ${props.duration / 1000}s ${props.easing}, opacity ${props.duration / 1000}s ${props.easing}`;
    requestAnimationFrame(() => {
      el.style.transform = 'translateX(0)';
      el.style.opacity = '1';
    });
  });
  const onEnd = (event) => {
    if (event.target === el && (event.propertyName === 'transform' || event.propertyName === 'opacity')) {
      el.removeEventListener('transitionend', onEnd);
      cleanup(el);
      unlockContainerHeight();
      done();
      emit('expand-end');
    }
  };
  el.addEventListener('transitionend', onEnd);
}

function leave(el, done) {
  if (props.disabled) {
    cleanup(el);
    unlockContainerHeight();
    done();
    return;
  }
  emit('collapse-start');
  lockContainerHeight();
  const distance = getSlideDistance();
  const finalX = slideDirection.value === 'right' ? -distance : distance;
  requestAnimationFrame(() => {
    el.style.transition = `transform ${props.duration / 1000}s ${props.easing}, opacity ${props.duration / 1000}s ${props.easing}`;
    requestAnimationFrame(() => {
      el.style.transform = `translateX(${finalX}px)`;
      el.style.opacity = '0';
    });
  });
  const onEnd = (event) => {
    if (event.target === el && (event.propertyName === 'transform' || event.propertyName === 'opacity')) {
      el.removeEventListener('transitionend', onEnd);
      cleanup(el);
      done();
    }
  };
  el.addEventListener('transitionend', onEnd);
}

function afterLeave() {
  emit('collapse-end');
}

function cleanup(el) {
  if (!el) return;
  el.style.transition = '';
  el.style.opacity = '';
  el.style.transform = '';
  el.style.willChange = '';
}

// slot 切換時鎖高度，避免閃爍
watch(showContent, async () => {
  if (container.value && !animationInProgress) {
    lockContainerHeight();
    await nextTick();
  }
});
</script>

<style scoped>
.container {
  transition: height 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: height;
  overflow: hidden;
  transform: translateZ(0);
}

.content {
  width: 100%;
  transform: translateZ(0);
  will-change: transform, opacity;
  white-space: normal;
}
</style>
