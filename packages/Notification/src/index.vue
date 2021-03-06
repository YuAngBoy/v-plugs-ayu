<template>
<transition name="ayu-notification-fade">
  <div
    :class="['ayu-notification', horizontalClass, `ayu-notification--${type}`]"
    v-if="visible"
    :style="positionStyle"
  >
    <div class="ayu-notification-icon">
      <ayu-icon :icon-class="icon" v-if="type" />
    </div>
    <div class="ayu-notification__group">
      <h2 class="ayu-notification__title" v-text="title"></h2>
      <p class="ayu-notification__content" v-html="content"></p>
    </div>
    <ayu-icon icon-class="close" class="ayu-close-btn" v-if="showClose" @click.native.stop="handleClose()"></ayu-icon>
  </div>
</transition>
</template>

<script>
import box from '../../mixins/box.js';
let typeIconMap = {
  success: 'success',
  info: 'info',
  warning: 'warning',
  error: 'error'
};
export default {
  name: 'AyuNotification',
  mixins: [box],
  props: {
    type: {
      type: String,
      default: ''
    },
    position: {
      type: String,
      default: 'top-right'
    },
    title: {
      type: String,
      default: ''
    },
    content: {
      type: String,
      default: ''
    },
    duration: { 
      type: Number, 
      default: 4500 
    },
    autoClose: { 
      type: Boolean, 
      default: true 
    },
    showClose: { 
      type: Boolean, 
      default: true 
    },
    offset: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      verticalOffset: 15,
      timer: null,
    }
  },
  computed: {
    horizontalClass() {
      return this.position.includes('right') ? 'right' : 'left';
    },
    icon() {
      return typeIconMap[this.type];
    },
    positionStyle() {
      return { top: `${this.verticalOffset + this.offset}px`};
    }
  },
  methods: {
    startTimer() {
      if (this.duration > 0) {
        this.timer = setTimeout(() => {
          this.close();
          this.$emit('notifyClose');
        }, this.duration);
      }
    },
    stopTimer() {
      if (this.timer) {
        clearTimeout(this.timer);
        this.timer = null;
      }
    },
    handleClose() {
      this.close();
      this.$emit('notifyClose');
    }
  },
  mounted() {
    if (this.autoClose) this.startTimer();
  },
  beforeDestroy() {
    this.stopTimer();
  }
}
</script>
