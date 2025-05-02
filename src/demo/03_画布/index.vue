<script setup lang="ts">
import { Graph } from '@antv/x6'
import { onMounted, ref } from 'vue';

const containerRef = ref()

onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    // 平移
    panning: true,
    // 缩放
    mousewheel: true,
    background: {
      color: '#F2F7FA',
    },
    grid: {
        visible: true,
        type: 'doubleMesh',
        args: [
          {
            color: '#eee', // 主网格线颜色
            thickness: 1, // 主网格线宽度
          },
          {
            color: '#ddd', // 次网格线颜色
            thickness: 1, // 次网格线宽度
            factor: 4, // 主次网格线间隔
          },
        ],
      },
  })
})
</script>

<template>
  <div id="container" ref="containerRef"></div>
</template>