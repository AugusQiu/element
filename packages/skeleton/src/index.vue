<template>
  <div>
    <template v-if="uiLoading">
      <div :class="['el-skeleton', animated ? 'is-animated' : '', ]" v-bind="$attrs">
        <template v-for="i in count">
          <slot v-if="loading" name="template">
            <el-skeleton-item
              v-for="item in rows"
              :key="`${i}-${item}`"
              :class="{
                'el-skeleton__paragraph': item !== 1,
                'is-first': item === 1,
                'is-last': item === rows && rows > 1,
              }"
              variant="p"
            />
            <!-- 
                多个class类名操控样式 精简两种写法
                :class="{ 'class1': true, 'class2': false }" 
                :class="['class1','class2', true ? 'class3': '']"
             -->
          </slot>
        </template>
      </div>
    </template>
    <!-- 默认default插槽 用来渲染真实展现的dom -->
    <template v-else> 
      <!-- 多层组件嵌套 属性透传 -->
      <slot v-bind="$attrs"></slot>
    </template>
  </div>
</template>
<script>
export default {
  name: 'ElSkeleton',
  props: {
    animated: {
      type: Boolean,
      default: false
    },
    count: {  
      type: Number,
      default: 1
    },
    rows: {
      type: Number,
      default: 4
    },
    loading: { // 是否显示骨架屏
      type: Boolean,
      default: true
    },
    throttle: { // 防抖，避免数据请求过快，骨架屏一闪而过
      type: Number,
      default: 0
    }
  },
  watch: {
    loading: {
      handler(loading) {
        // 不设置防抖延迟，loading就根据接口请求的时间，来切换显示状态
        if (this.throttle <= 0) {
          this.uiLoading = loading;
          return;
        }
        if (loading) {
          clearTimeout(this.timeoutHandle);
          this.timeoutHandle = setTimeout(() => {
            this.uiLoading = this.loading;
          }, this.throttle); // 骨架屏至少显示 throttle 毫秒
        } else {
          this.uiLoading = loading;
        }
      },
      immediate: true
    }
  },
  data() {
    return {
      uiLoading: this.throttle <= 0 ? this.loading : false
    };
  }
};
</script>
