<template>
  <transition :css="false" mode="out-in" @before-enter="beforeEnter" @enter="enter" @leave="leave"
    @after-leave="afterLeave" @enter-cancelled="cleanup" @leave-cancelled="cleanup">
    <slot />
  </transition>
</template>

<script setup>
const props = defineProps({
  duration: {
    type: Number,
    default: 300
  },
  easing: {
    type: String,
    default: 'ease-in-out' // 簡單自然的緩動曲線
  },
  disabled: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['expand-start', 'expand-end', 'collapse-start', 'collapse-end']);

function beforeEnter(el) {
  el.style.opacity = '0';
  emit('expand-start');
}

function enter(el, done) {
  if (props.disabled) {
    cleanup(el);
    done();
    return;
  }
  el.style.transition = `opacity ${props.duration / 1000}s ${props.easing}`;
  el.style.opacity = '1';
  setTimeout(() => {
    cleanup(el);
    done();
    emit('expand-end');
  }, props.duration);
}

function leave(el, done) {
  if (props.disabled) {
    cleanup(el);
    done();
    return;
  }
  emit('collapse-start');
  el.style.transition = `opacity ${props.duration / 1000}s ${props.easing}`;
  el.style.opacity = '0';
  setTimeout(() => {
    done();
  }, props.duration);
}

function afterLeave(el) {
  cleanup(el);
  emit('collapse-end');
}

function cleanup(el) {
  if (!el) return;
  el.style.transition = '';
  el.style.opacity = '';
}
</script>
