<template>
  <div class="pptist-editor">
    <!-- 编辑器头部 -->
    <EditorHeader class="layout-header" />
    <div class="layout-content">
      <!-- 左侧缩略图区域 -->
      <Thumbnails class="layout-content-left" />
      <div class="layout-content-center">
        <!-- 画布工具栏 -->
        <CanvasTool class="center-top" />
        <!-- 主画布区域 -->
        <Canvas
          class="center-body"
          :style="{ height: `calc(100% - ${remarkHeight + 40}px)` }"
        />
        <!-- 备注区域 -->
        <Remark
          class="center-bottom"
          v-model:height="remarkHeight"
          :style="{ height: `${remarkHeight}px` }"
        />
      </div>
      <!-- 右侧工具栏 -->
      <Toolbar class="layout-content-right" />
    </div>
  </div>

  <!-- 选择面板 -->
  <SelectPanel v-if="showSelectPanel" />
  <!-- 搜索面板 -->
  <SearchPanel v-if="showSearchPanel" />
  <!-- 笔记面板 -->
  <NotesPanel v-if="showNotesPanel" />

  <!-- 导出对话框 -->
  <Modal
    :visible="!!dialogForExport"
    :width="680"
    @closed="closeExportDialog()"
  >
    <ExportDialog />
  </Modal>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { storeToRefs } from 'pinia';
import { useMainStore } from '@/store';
import useGlobalHotkey from '@/hooks/useGlobalHotkey';
import usePasteEvent from '@/hooks/usePasteEvent';

// 导入各个组件
import EditorHeader from './EditorHeader/index.vue';
import Canvas from './Canvas/index.vue';
import CanvasTool from './CanvasTool/index.vue';
import Thumbnails from './Thumbnails/index.vue';
import Toolbar from './Toolbar/index.vue';
import Remark from './Remark/index.vue';
import ExportDialog from './ExportDialog/index.vue';
import SelectPanel from './SelectPanel.vue';
import SearchPanel from './SearchPanel.vue';
import NotesPanel from './NotesPanel.vue';
import Modal from '@/components/Modal.vue';

// 定义组件名称
defineOptions({
  name: 'Editor'
});

// 使用主存储
const mainStore = useMainStore();
// 从存储中解构需要的状态
const { dialogForExport, showSelectPanel, showSearchPanel, showNotesPanel } =
  storeToRefs(mainStore);
// 关闭导出对话框的方法
const closeExportDialog = () => mainStore.setDialogForExport('');

// 备注区域的高度，默认为40px
const remarkHeight = ref(40);

// 使用全局热键
useGlobalHotkey();
// 使用粘贴事件
usePasteEvent();
</script>

<style lang="scss" scoped>
.pptist-editor {
  height: 100%;
}
.layout-header {
  height: 40px;
}
.layout-content {
  height: calc(100% - 40px);
  display: flex;
}
.layout-content-left {
  width: 160px;
  height: 100%;
  flex-shrink: 0;
}
.layout-content-center {
  width: calc(100% - 160px - 260px);

  .center-top {
    height: 40px;
  }
}
.layout-content-right {
  width: 260px;
  height: 100%;
}
</style>
