<script setup lang="ts">
import { Dropdown, Menu, MenuItem } from 'ant-design-vue';
import { inject } from 'vue';

const getNode = inject('getNode') as unknown as any
const node = getNode()
console.log(node)
</script>

<template>
  <Dropdown trigger="contextmenu">
    <template #overlay>
      <Menu>
        <MenuItem>
          <a href="javascript:;">删除</a>
        </MenuItem>
        <MenuItem>
          <a href="javascript:;">属性</a>
        </MenuItem>
        <MenuItem>
          <a href="javascript:;">编辑</a>
        </MenuItem>
      </Menu>
    </template>
    <div class="cm">
      <img src="../../../../public/vite.svg" alt="">
    </div>
  </Dropdown>
</template>

<style scoped>
.cm {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  background-color: #fff;
  border: 1px dashed #e74d62;
  border-radius: 100%;
}
</style>