<template>
  <div
    ref="list"
    v-bind="$attrs"
    v-feedback="feedbackClass"
    :class="listCls"
    :disabled="listDisabled"
    @click="handleClick"
  >
    <div v-if="icon||$slots.thumb" :class="`${prefixCls}-thumb`">
      <v-icon v-if="icon" :type="icon"/>
      <slot name="thumb"></slot>
    </div>
    <div :class="lineCls">
      <div v-if="$slots.default" :class="`${prefixCls}-content`">
        <slot name="default"></slot>
        <div v-if="brief" :class="`${prefixCls}-brief`">
          {{this.brief}}
        </div>
      </div>
      <div v-if="$slots.extra" :class="`${prefixCls}-extra`">
        <slot name="extra"></slot>
      </div>
      <div v-if="arrow" :class="arrowCls" :aria-hidden="true"></div>
    </div>
  </div>
</template>

<script>
  import Vue from 'vue';
  import VFeedback from 'v-feedback';

  Vue.use(VFeedback);

  const prefixCls = 'vm-list';

  export default {
    name: 'VListItem',
    props: {
      arrow: String,
      icon: String,
      error: Boolean, // 右侧是否显示错误样式
      align: {  // 子元素垂直对齐
        type: String,
        default: 'middle'
      },
      wrap: Boolean, // 是否换行，默认文字超出被隐藏
      multipleLine: Boolean, // 是否多行
      brief: String, // 描述文案
      activeClass: {
        type: String
      }, // 激活样式类名
      disabled: Boolean
    },
    watch: {
      // 监听activeClass变化
      activeClass: function(val) {
        const list = this.$refs.list;
        if (list) {
          this.$refs.list.setAttribute('data-feedback-class', val);
        }
      },
      // 监听disabled变化
      disabled: function(val) {
        const list = this.$refs.list;
        if (list) {
          this.$refs.list.setAttribute('data-feedback-class', val ? 'none-feedback' : this.activeClass);
        }
      }
    },
    computed: {
      feedbackClass() {
        return !this.listDisabled && (this.$listeners.click || this.$listeners.touchstart)
          ? this.activeClass || 'e-feedback' : 'no-feedback';
      },
      listDisabled() {
        return this.disabled;
      },
      listCls() {
        return {
          [`${prefixCls}-item`]: true,
          [`${prefixCls}-item-disabled`]: this.disabled,
          [`${prefixCls}-item-error`]: this.error,
          [`${prefixCls}-item-top`]: this.align === 'top',
          [`${prefixCls}-item-middle`]: this.align === 'middle',
          [`${prefixCls}-item-bottom`]: this.align === 'bottom'
        };
      },
      lineCls() {
        return {
          [`${prefixCls}-line`]: true,
          [`${prefixCls}-line-multiple`]: this.multipleLine,
          [`${prefixCls}-line-wrap`]: this.wrap
        };
      },
      arrowCls() {
        return {
          [`${prefixCls}-arrow`]: true,
          [`${prefixCls}-arrow-horizontal`]: this.arrow === 'horizontal',
          [`${prefixCls}-arrow-vertical`]: this.arrow === 'down' || this.arrow === 'up',
          [`${prefixCls}-arrow-vertical-up`]: this.arrow === 'up'
        };
      }
    },
    data() {
      return {
        prefixCls: prefixCls
      };
    },
    methods: {
      handleClick(event) {
        if (!this.disabled) {
          this.$emit('click', event);
        }
      }
    }
  };
</script>
