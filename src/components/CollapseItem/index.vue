<template>
  <div class="collapse-item">
    <div class="collapse-header" @click="onToggle">
      <div class="header-title">{{ title }}</div>
      <div class="header-icon">
        {{ isCollapse ? '⬆️' : '⬇️' }}
      </div>
    </div>

    <div
      ref="contentRef"
      class="collapse-content"
      :style="contentStyle"
    >
      <div class="content-inner" ref="innerRef">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CollapseItem',

  props: {
    title: String,
    config: {
      type: Object,
      default: () => ({
        minHeight: 20,          // 收起后保留的高度
        bounceOffset: 10,       // 回弹偏移量
        duration: 500,          // 动画总时长
        opacityDuration: 100    // 透明度动画时长
      })
    }
  },

  data() {
    return {
      isCollapse: false,  // 默认展开
      contentHeight: 0,
      isAnimating: false,
      animationId: null,
      currentHeight: 0,
      currentOpacity: 1,
      // 缓动函数
      easingFunctions: {
        easeOutQuad: (t) => t * (2 - t),
        easeInOutQuad: (t) => t < 0.5 ? 2 * t * t : -1 + (4 - 2 * t) * t,
        easeOutElastic: (t) => {
          const p = 0.3;
          return Math.pow(2, -10 * t) * Math.sin((t - p / 4) * (2 * Math.PI) / p) + 1;
        }
      },
    };
  },

  computed: {
    contentStyle() {
      return {
        height: `${this.currentHeight}px`,
        opacity: this.currentOpacity,
        overflow: 'hidden',
        transition: 'none'  // 禁用CSS过渡，完全由JS控制
      };
    }
  },

  mounted() {
    // 初始测量内容高度
    this.$nextTick(() => {
      this.measureContentHeight();
      // 初始展开状态
      this.currentHeight = this.contentHeight;
    });

    // 监听内容变化
    this.setupResizeObserver();
  },

  beforeDestroy() {
    this.cancelAnimation();
    if (this.resizeObserver) {
      this.resizeObserver.disconnect();
    }
  },

  methods: {
    cancelAnimation() {
      if (this.animationId) {
        cancelAnimationFrame(this.animationId);
        this.animationId = null;
      }
      this.isAnimating = false;
    },

    onToggle() {
      if (this.isAnimating) return;

      // 更新状态
      this.isCollapse = !this.isCollapse;

      // 收起时立即开始动画
      if (this.isCollapse) {
        this.collapse();
      } else {
        // 展开前重新测量高度
        this.measureContentHeight();
        this.expand();
      }
    },

    measureContentHeight() {
      if (this.$refs.innerRef) {
        const innerEl = this.$refs.innerRef;
        // 获取准确的包含padding的内容高度
        const computedStyle = window.getComputedStyle(innerEl);
        const height = innerEl.scrollHeight;
        const paddingTop = parseFloat(computedStyle.paddingTop) || 0;
        const paddingBottom = parseFloat(computedStyle.paddingBottom) || 0;
        this.contentHeight = height + paddingTop + paddingBottom;
      }
    },

    setupResizeObserver() {
      if ('ResizeObserver' in window) {
        this.resizeObserver = new ResizeObserver(() => {
          // 只有在展开状态下才重新测量
          if (!this.isCollapse) {
            this.measureContentHeight();
            // 如果没有动画在进行，更新当前高度
            if (!this.isAnimating) {
              this.currentHeight = this.contentHeight;
            }
          }
        });

        if (this.$refs.innerRef) {
          this.resizeObserver.observe(this.$refs.innerRef);
        }
      }
    },

    // 展开动画
    expand() {
      this.cancelAnimation();
      this.isAnimating = true;

      const startHeight = this.config.minHeight;
      const targetHeight = this.contentHeight;
      const bounceHeight = targetHeight + this.config.bounceOffset;
      const totalDuration = this.config.duration;
      const opacityDuration = this.config.opacityDuration;

      let startTime = null;
      const animate = (timestamp) => {
        if (!startTime) startTime = timestamp;
        const elapsed = timestamp - startTime;
        const progress = Math.min(elapsed / totalDuration, 1);

        let currentHeight;
        let currentOpacity;

        // 两阶段动画
        if (progress < 0.5) {
          // 第一阶段：展开到回弹高度
          const stageProgress = progress * 2;
          const easedProgress = this.easingFunctions.easeOutQuad(stageProgress);
          currentHeight = startHeight + (bounceHeight - startHeight) * easedProgress;
        } else {
          // 第二阶段：回弹到实际高度
          const stageProgress = (progress - 0.5) * 2;
          const easedProgress = this.easingFunctions.easeOutElastic(stageProgress);
          currentHeight = bounceHeight + (targetHeight - bounceHeight) * easedProgress;
        }

        // 透明度动画
        const opacityProgress = Math.min(elapsed / opacityDuration, 1);
        currentOpacity = this.easingFunctions.easeOutQuad(opacityProgress);

        // 更新当前值
        this.currentHeight = currentHeight;
        this.currentOpacity = currentOpacity;

        if (progress < 1) {
          this.animationId = requestAnimationFrame(animate);
        } else {
          // 动画完成
          this.currentHeight = targetHeight;
          this.currentOpacity = 1;
          this.animationId = null;
          this.isAnimating = false;
        }
      };

      // 设置初始状态
      this.currentHeight = startHeight;
      this.currentOpacity = 0;

      // 开始动画
      this.animationId = requestAnimationFrame(animate);
    },

    // 收起动画
    collapse() {
      this.cancelAnimation();
      this.isAnimating = true;

      const startHeight = this.contentHeight;
      const minHeight = this.config.minHeight;
      const bounceHeight = Math.max(minHeight - this.config.bounceOffset, 0);
      const totalDuration = this.config.duration;
      const opacityDuration = this.config.opacityDuration;

      let startTime = null;
      const animate = (timestamp) => {
        if (!startTime) startTime = timestamp;
        const elapsed = timestamp - startTime;
        const progress = Math.min(elapsed / totalDuration, 1);

        let currentHeight;
        let currentOpacity;

        // 两阶段动画
        if (progress < 0.5) {
          // 第一阶段：收到回弹高度
          const stageProgress = progress * 2;
          const easedProgress = this.easingFunctions.easeOutQuad(stageProgress);
          currentHeight = startHeight + (bounceHeight - startHeight) * easedProgress;
        } else {
          // 第二阶段：回弹到最小高度
          const stageProgress = (progress - 0.5) * 2;
          const easedProgress = this.easingFunctions.easeOutElastic(stageProgress);
          currentHeight = bounceHeight + (minHeight - bounceHeight) * easedProgress;
        }

        // 透明度动画
        const opacityProgress = Math.max(1 - elapsed / opacityDuration, 0);
        currentOpacity = this.easingFunctions.easeOutQuad(opacityProgress);

        // 更新当前值
        this.currentHeight = currentHeight;
        this.currentOpacity = currentOpacity;

        if (progress < 1) {
          this.animationId = requestAnimationFrame(animate);
        } else {
          // 动画完成
          this.currentHeight = minHeight;
          this.currentOpacity = 0.01; // 保持一点点透明度，避免完全消失
          this.animationId = null;
          this.isAnimating = false;
        }
      };

      // 设置初始状态
      this.currentHeight = startHeight;
      this.currentOpacity = 1;

      // 开始动画
      this.animationId = requestAnimationFrame(animate);
    },

    // 外部可调用的刷新方法
    refresh() {
      this.measureContentHeight();
      if (!this.isCollapse && !this.isAnimating) {
        this.currentHeight = this.contentHeight;
      }
    }
  }
};
</script>

<style scoped>
.collapse-item {
  margin-bottom: 12px;
  border-radius: 8px;
  overflow: hidden;
  background: white;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  transition: box-shadow 0.3s ease;
}

.collapse-item:hover {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
}

.collapse-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 48px;
  padding: 0 20px;
  background: linear-gradient(90deg, #409EFF 0%, #67C23A 100%);
  color: white;
  font-weight: 500;
  cursor: pointer;
  user-select: none;
  transition: background 0.3s ease;
}

.collapse-header:hover {
  background: linear-gradient(90deg, #66b1ff 0%, #85ce61 100%);
}

.header-title {
  font-size: 16px;
  font-weight: 600;
}

.header-icon {
  font-size: 14px;
  transition: transform 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.header-icon.is-collapsed {
  transform: rotate(180deg);
}

.collapse-content {
  /* 注意：这里没有transition，完全由JS控制 */
  overflow: hidden;

  /* 强制GPU加速 */
  transform: translateZ(0);
  will-change: height, opacity;
  backface-visibility: hidden;
  -webkit-font-smoothing: antialiased;
  contain: content;
}

.content-inner {
  padding: 20px;
  transform: translateZ(0);
  backface-visibility: hidden;
}

/* 性能优化 */
.collapse-content * {
  backface-visibility: hidden;
  -webkit-font-smoothing: antialiased;
}
</style>


<!--<template>-->
<!--  <div class="collapse-item">-->
<!--    <div class="collapse-header">-->
<!--      <div>{{ title }}</div>-->
<!--      <el-button size="mini" @click="onToggle">{{ isCollapse ? '⬆️' : '⬇️' }}</el-button>-->
<!--    </div>-->
<!--    <div class="collapse-content">-->
<!--      <slot></slot>-->
<!--    </div>-->
<!--  </div>-->
<!--</template>-->

<!--<script>-->
<!--export default {-->
<!--  name: 'CollapseItem',-->
<!--  props: {-->
<!--    title: String,-->
<!--  },-->
<!--  data() {-->
<!--    return {-->
<!--      isCollapse: false,-->
<!--    }-->
<!--  },-->
<!--  methods: {-->
<!--    onToggle() {-->
<!--      this.isCollapse = !this.isCollapse-->
<!--    },-->
<!--  }-->
<!--}-->
<!--</script>-->

<!--<style scoped>-->
<!--.collapse-header {-->
<!--  display: flex;-->
<!--  justify-content: space-between;-->
<!--  align-items: center;-->
<!--  height: 40px;-->
<!--  padding: 0 10px;-->
<!--  background-color: lightblue;-->
<!--}-->

<!--.collapse-content {-->
<!--  padding: 10px;-->
<!--  border: 1px solid lightgray;-->
<!--}-->
<!--</style>-->
