<template>
  <!-- template里面可以直接放两个标签 -->
  <router-view />
</template>

<script lang="ts">
import { ref, provide } from 'vue';
import { router } from './router';
export default {
  name: 'App',
  setup() {
    // JS获取页面宽度来确定值
    const width = document.documentElement.clientWidth;
    const menuVisible = ref(width <= 500 ? false : true);

    // 可以让子组件都能能访问到这个变量
    provide('menuVisible', menuVisible); // set

    // 路由切换时
    router.afterEach(() => {
      if (width <= 500) {
        menuVisible.value = false;
      }
    });
  },
};
</script>
