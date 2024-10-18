<template>
  <div class="pptist-screen">
    <!-- 根据viewMode显示不同的视图组件 -->
    <BaseView :changeViewMode="changeViewMode" v-if="viewMode === 'base'" />
    <PresenterView
      :changeViewMode="changeViewMode"
      v-else-if="viewMode === 'presenter'"
    />
  </div>
</template>

<script lang="ts" setup>
import { onMounted, onUnmounted, ref } from 'vue';
import { KEYS } from '@/configs/hotkey';
import useScreening from '@/hooks/useScreening';

import BaseView from './BaseView.vue';
import PresenterView from './PresenterView.vue';

// 定义组件名称
defineOptions({
  name: 'Screen'
});

// 定义视图模式，可以是'base'或'presenter'
const viewMode = ref<'base' | 'presenter'>('base');

// 切换视图模式的函数
const changeViewMode = (mode: 'base' | 'presenter') => {
  viewMode.value = mode;
};

const { exitScreening } = useScreening();

// 监听键盘事件，用于退出放映
const keydownListener = (e: KeyboardEvent) => {
  const key = e.key.toUpperCase();
  if (key === KEYS.ESC) exitScreening();
};

// 组件挂载时添加键盘事件监听器
onMounted(() => document.addEventListener('keydown', keydownListener));
// 组件卸载时移除键盘事件监听器
onUnmounted(() => document.removeEventListener('keydown', keydownListener));
</script>

<style lang="scss" scoped>
.pptist-screen {
  width: 100%;
  height: 100%;
}
</style>
