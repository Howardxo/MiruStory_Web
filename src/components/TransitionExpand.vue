<template>
    <transition
      :name="transitionName"
      @enter="enter"
      @after-enter="afterEnter"
      @leave="leave"
      @after-leave="afterLeave"
      @enter-cancelled="resetStyles"
      @leave-cancelled="resetStyles"
    >
      <slot />
    </transition>
  </template>
  
  <script>
  export default {
    name: "TransitionExpand",
    props: {
      // 自定義持續時間（毫秒）
      duration: {
        type: Number,
        default: 300
      },
      // 自定義緩動函數
      easing: {
        type: String,
        default: 'ease-in-out'
      },
      // 自定義過渡名稱
      transitionName: {
        type: String,
        default: 'expand'
      },
      // 是否禁用過渡
      disabled: {
        type: Boolean,
        default: false
      }
    },
    
    emits: ['expand-start', 'expand-end', 'collapse-start', 'collapse-end'],
    
    methods: {
      // 測量元素的自然高度
      measureHeight(element) {
        try {
          // 保存當前樣式以便還原
          const originalStyles = {
            position: element.style.position,
            visibility: element.style.visibility,
            height: element.style.height,
            width: element.style.width,
            overflow: element.style.overflow
          };
          
          // 設置樣式以便測量
          element.style.position = "absolute";
          element.style.visibility = "hidden";
          element.style.height = "auto";
          element.style.width = getComputedStyle(element).width;
          element.style.overflow = "hidden";
          
          // 測量實際高度
          const height = getComputedStyle(element).height;
          
          // 還原原始樣式
          Object.keys(originalStyles).forEach(key => {
            element.style[key] = originalStyles[key];
          });
          
          return height;
        } catch (error) {
          console.error("Error measuring element height:", error);
          return "auto"; // 發生錯誤時的後備值
        }
      },
      
      // 設置過渡樣式
      setTransitionStyles(element) {
        if (this.disabled) return;
        
        const duration = `${this.duration / 1000}s`;
        element.style.transition = `height ${duration} ${this.easing}`;
        element.style.overflow = "hidden";
      },
      
      // 重置所有添加的樣式
      resetStyles(element) {
        if (!element) return;
        
        element.style.transition = '';
        element.style.height = '';
        element.style.overflow = '';
      },
      
      // 元素進入過渡
      enter(element) {
        if (this.disabled) {
          element.style.height = "auto";
          return;
        }
        
        this.$emit('expand-start');
        
        // 測量目標高度
        const height = this.measureHeight(element);
        
        // 設置初始高度為0
        element.style.height = '0';
        
        // 強制重繪
        getComputedStyle(element).height;
        
        // 設置過渡
        this.setTransitionStyles(element);
        
        // 開始過渡到目標高度
        requestAnimationFrame(() => {
          element.style.height = height;
        });
      },
      
      // 進入過渡完成後
      afterEnter(element) {
        this.resetStyles(element);
        element.style.height = "auto";
        this.$emit('expand-end');
      },
      
      // 元素離開過渡
      leave(element) {
        if (this.disabled) {
          element.style.height = '0';
          return;
        }
        
        this.$emit('collapse-start');
        
        // 設置當前實際高度
        element.style.height = getComputedStyle(element).height;
        
        // 強制重繪
        getComputedStyle(element).height;
        
        // 設置過渡
        this.setTransitionStyles(element);
        
        // 開始過渡到高度0
        requestAnimationFrame(() => {
          element.style.height = '0';
        });
      },
      
      // 離開過渡完成後
      afterLeave(element) {
        this.resetStyles(element);
        this.$emit('collapse-end');
      }
    }
  };
  </script>
  
  <style>
  /* 注意：這些樣式只在沒有JS的情況下作為後備方案 */
  .expand-enter-active,
  .expand-leave-active {
    transition: height 0.3s ease-in-out;
    overflow: hidden;
  }
  
  .expand-enter-from,
  .expand-leave-to {
    height: 0;
  }
  </style>
  