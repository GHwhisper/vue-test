<template>
  <div class="collapse-item">
    <div class="collapse-header" @click="onToggle">
      <div class="header-title">{{ title }}</div>
      <div class="header-icon" :class="{ 'is-collapsed': isCollapse }">
        {{ isCollapse ? '⬇️' : '⬆️' }}
      </div>
    </div>

    <!-- 使用CSS transition替代JS动画 -->
    <div
      ref="contentRef"
      class="collapse-content"
      :class="{ 'is-expanded': !isCollapse }"
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
        minHeight: 20,
        bounceOffset: 10,
        duration: 500,
        opacityDuration: 100
      })
    }
  },

  data() {
    return {
      isCollapse: true,
      contentHeight: 0,
      isMeasuring: false,
      isTransitioning: false
    };
  },

  computed: {
    contentStyle() {
      return {
        '--min-height': `${this.config.minHeight}px`,
        '--bounce-offset': `${this.config.bounceOffset}px`,
        '--duration': `${this.config.duration}ms`,
        '--opacity-duration': `${this.config.opacityDuration}ms`,
        '--max-height': this.isCollapse ? '0px' : `${this.contentHeight}px`
      };
    }
  },

  mounted() {
    // 测量内容高度
    this.measureContentHeight();

    // 监听内容变化
    this.setupResizeObserver();
  },

  beforeDestroy() {
    if (this.resizeObserver) {
      this.resizeObserver.disconnect();
    }
  },

  methods: {
    onToggle() {
      if (this.isTransitioning) return;

      if (!this.isCollapse) {
        // 收起时直接切换类，触发CSS动画
        this.isCollapse = true;
      } else {
        // 展开前确保测量了正确的高度
        this.measureContentHeight();
        this.isCollapse = false;
      }

      this.isTransitioning = true;

      // 动画结束后重置状态
      setTimeout(() => {
        this.isTransitioning = false;
      }, this.config.duration);
    },

    measureContentHeight() {
      // 使用异步避免阻塞主线程
      requestAnimationFrame(() => {
        if (this.$refs.innerRef) {
          // 获取实际内容高度
          this.contentHeight = this.$refs.innerRef.scrollHeight;
        }
      });
    },

    setupResizeObserver() {
      if (typeof ResizeObserver !== 'undefined') {
        this.resizeObserver = new ResizeObserver(() => {
          // 防抖处理，避免频繁测量
          if (this.measureTimer) {
            clearTimeout(this.measureTimer);
          }

          this.measureTimer = setTimeout(() => {
            if (!this.isCollapse) {
              this.measureContentHeight();
            }
          }, 50);
        });

        if (this.$refs.innerRef) {
          this.resizeObserver.observe(this.$refs.innerRef);
        }
      }
    },

    // 外部可调用的刷新方法
    refresh() {
      this.measureContentHeight();
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
  /* 使用max-height和opacity实现动画 */
  max-height: 0;
  opacity: 0;
  overflow: hidden;

  /* 启用GPU加速 */
  transform: translateZ(0);
  backface-visibility: hidden;
  perspective: 1000;

  /* 使用will-change提示浏览器 */
  will-change: max-height, opacity;

  /* 过渡效果 */
  transition-property: max-height, opacity;
  transition-timing-function: cubic-bezier(0.34, 1.56, 0.64, 1);
  transition-duration: var(--duration);
}

/* 展开状态 */
.collapse-content.is-expanded {
  max-height: var(--max-height);
  opacity: 1;

  /* 展开时的特殊处理：使用动画关键帧实现回弹效果 */
  animation: expandAnimation var(--duration) cubic-bezier(0.34, 1.56, 0.64, 1);
}

/* 收起状态 */
.collapse-content:not(.is-expanded) {
  max-height: 0;
  opacity: 0;

  /* 收起时的特殊处理：使用动画关键帧实现回弹效果 */
  animation: collapseAnimation var(--duration) cubic-bezier(0.34, 1.56, 0.64, 1);
}

.content-inner {
  padding: 20px;
  opacity: 1;
  transition: opacity var(--opacity-duration) ease;
  transform: translateZ(0);
  backface-visibility: hidden;
}

/* 收起时内容透明度快速降低 */
.collapse-content:not(.is-expanded) .content-inner {
  opacity: 0.01;
  transition: opacity var(--opacity-duration) ease;
}

/* 展开时内容透明度快速恢复 */
.collapse-content.is-expanded .content-inner {
  opacity: 1;
  transition: opacity var(--opacity-duration) ease;
}

/* 关键帧动画实现回弹效果 */
@keyframes expandAnimation {
  0% {
    max-height: var(--min-height);
    opacity: 0;
  }
  50% {
    max-height: calc(var(--max-height) + var(--bounce-offset));
    opacity: 1;
  }
  100% {
    max-height: var(--max-height);
    opacity: 1;
  }
}

@keyframes collapseAnimation {
  0% {
    max-height: var(--max-height);
    opacity: 1;
  }
  50% {
    max-height: calc(var(--min-height) - var(--bounce-offset));
    opacity: 0.5;
  }
  100% {
    max-height: var(--min-height);
    opacity: 0;
  }
}

/* 强制GPU加速和优化 */
.collapse-content {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  contain: content; /* 限制浏览器重绘范围 */
}

/* 性能优化：禁用动画期间的事件 */
.collapse-content.is-transitioning {
  pointer-events: none;
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
