<template>
  <!-- 根据条件渲染不同的组件 -->
  <Screen v-if="screening" />
  <Editor v-else-if="_isPC" />
  <Mobile v-else />
</template>

<script lang="ts" setup>
// 导入所需的 Vue 和 Pinia 功能
import { onMounted } from 'vue';
import { storeToRefs } from 'pinia';

// 导入自定义 store
import { useScreenStore, useMainStore, useSnapshotStore } from '@/store';

// 导入常量和工具函数
import { LOCALSTORAGE_KEY_DISCARDED_DB } from '@/configs/storage';
import { deleteDiscardedDB } from '@/utils/database';
import { isPC } from './utils/common';

// 导入组件
import Editor from './views/Editor/index.vue';
import Screen from './views/Screen/index.vue';
import Mobile from './views/Mobile/index.vue';

// 判断是否为 PC 设备
const _isPC = isPC();

// 初始化 store
const mainStore = useMainStore();
const snapshotStore = useSnapshotStore();

// 从 store 中获取响应式引用
const { databaseId } = storeToRefs(mainStore);
const { screening } = storeToRefs(useScreenStore());

// 在非开发环境下阻止用户刷新或关闭页面
if (import.meta.env.MODE !== 'development') {
  window.onbeforeunload = () => false;
}

// 组件挂载后执行的操作
onMounted(async () => {
  // 删除已废弃的数据库
  await deleteDiscardedDB();
  // 初始化快照数据库
  snapshotStore.initSnapshotDatabase();
  // 设置可用字体
  mainStore.setAvailableFonts();
});

// 应用注销时向 localStorage 中记录本次 indexedDB 的数据库ID，用于之后清除数据库
window.addEventListener('unload', () => {
  // 获取已废弃数据库列表
  const discardedDB = localStorage.getItem(LOCALSTORAGE_KEY_DISCARDED_DB);
  const discardedDBList: string[] = discardedDB ? JSON.parse(discardedDB) : [];

  // 将当前数据库 ID 添加到废弃列表中
  discardedDBList.push(databaseId.value);

  // 更新 localStorage 中的废弃数据库列表
  const newDiscardedDB = JSON.stringify(discardedDBList);
  localStorage.setItem(LOCALSTORAGE_KEY_DISCARDED_DB, newDiscardedDB);
});
</script>

<style lang="scss">
#app {
  height: 100%;
}
</style>
