<template>
    <transition
      :name="transitionName"
      @enter="(el, done) => enter(el as HTMLElement, done)"
      @after-enter="(el) => afterEnter(el as HTMLElement)"
      @leave="(el, done) => leave(el as HTMLElement, done)"
      @after-leave="(el) => afterLeave(el as HTMLElement)"
      @enter-cancelled="(el) => el && resetStyles(el as HTMLElement)"
      @leave-cancelled="(el) => el && resetStyles(el as HTMLElement)"
    >
      <slot />
    </transition>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType } from 'vue';
  
  export default defineComponent({
    name: "TransitionExpand",
    props: {
      duration: {
        type: Number as PropType<number>,
        default: 300
      },
      easing: {
        type: String as PropType<string>,
        default: 'ease-in-out'
      },
      transitionName: {
        type: String as PropType<string>,
        default: 'expand'
      },
      disabled: {
        type: Boolean as PropType<boolean>,
        default: false
      }
    },
    emits: ['expand-start', 'expand-end', 'collapse-start', 'collapse-end'],
    methods: {
      measureHeight(element: HTMLElement): string {
        try {
          const originalStyles = {
            position: element.style.position,
            visibility: element.style.visibility,
            height: element.style.height,
            width: element.style.width,
            overflow: element.style.overflow
          };
          element.style.position = "absolute";
          element.style.visibility = "hidden";
          element.style.height = "auto";
          element.style.width = getComputedStyle(element).width;
          element.style.overflow = "hidden";
          const height = getComputedStyle(element).height;
          Object.entries(originalStyles).forEach(([key, value]) => {
            (element.style as any)[key] = value;
          });
          return height;
        } catch (error) {
          console.error("測量元素高度時發生錯誤:", error);
          return "auto";
        }
      },
      setTransitionStyles(element: HTMLElement): void {
        if (this.disabled) return;
        const duration = `${this.duration / 1000}s`;
        element.style.transition = `height ${duration} ${this.easing}`;
        element.style.overflow = "hidden";
      },
      resetStyles(element?: HTMLElement): void {
        if (!element) return;
        element.style.transition = '';
        element.style.height = '';
        element.style.overflow = '';
      },
      enter(element: HTMLElement, done: () => void): void {
        if (this.disabled) {
          element.style.height = "auto";
          done();
          return;
        }
        this.$emit('expand-start');
        const height = this.measureHeight(element);
        element.style.height = '0';
        void element.offsetHeight;
        this.setTransitionStyles(element);
        requestAnimationFrame(() => {
          element.style.height = height;
          setTimeout(done, this.duration);
        });
      },
      afterEnter(element: HTMLElement): void {
        this.resetStyles(element);
        element.style.height = "auto";
        this.$emit('expand-end');
      },
      leave(element: HTMLElement, done: () => void): void {
        if (this.disabled) {
          element.style.height = '0';
          done();
          return;
        }
        this.$emit('collapse-start');
        element.style.height = getComputedStyle(element).height;
        void element.offsetHeight;
        this.setTransitionStyles(element);
        requestAnimationFrame(() => {
          element.style.height = '0';
          setTimeout(done, this.duration);
        });
      },
      afterLeave(element: HTMLElement): void {
        this.resetStyles(element);
        this.$emit('collapse-end');
      }
    }
  });
  </script>
  
  <style scoped>
  :deep(.expand-enter-active),
  :deep(.expand-leave-active) {
    transition: height 0.3s ease-in-out;
    overflow: hidden;
  }
  :deep(.expand-enter-from),
  :deep(.expand-leave-to) {
    height: 0;
  }
  </style>
  