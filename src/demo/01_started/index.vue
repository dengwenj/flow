<script setup lang="ts">
import { Graph } from '@antv/x6'
import { Snapline } from '@antv/x6-plugin-snapline';
import { onMounted, ref } from 'vue';

const containerRef = ref()

onMounted(() => {
  // 创建画布
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    background: {
      color: '#F2F7FA'
    }
  })

  graph.use(
    new Snapline({
      enabled: true,
    }),
  )

  const data = {
    // 节点
    nodes: [
      {
        id: 'node1',
        shape: 'rect',
        x: 40,
        y: 40,
        width: 100,
        height: 40,
        label: 'hello',
        attrs: {
          // body 是选择器名称，选中的是 rect 元素
          body: {
            // 边框
            stroke: '#8f8f8f',
            // 边框宽度
            strokeWidth: 1,
            // 背景色
            fill: '#fff',
            // 圆角
            rx: 6,
            ry: 6,
          },
        },
      },
      {
        id: 'node2',
        shape: 'rect',
        x: 300,
        y: 40,
        width: 100,
        height: 40,
        label: 'world',
        attrs: {
          body: {
            stroke: '#8f8f8f',
            strokeWidth: 1,
            fill: '#fff',
            rx: 6,
            ry: 6,
          },
        },
      },
    ],
    // 边，就是线
    edges: [
      {
        shape: 'edge',
        // 从哪个开始
        source: 'node1',
        // 目标
        target: 'node2',
        // 边的文字
        labels: [
          {
            position: {
              distance: 0.4, // 边长度的比例，0 表示起点，1 表示终点
              offset: { x: 20, y: 16 }, // 相对于默认位置的偏移量
            },
            attrs: {
              text: {
                text: 'Edge Label',
                fill: 'red', // 设置文字颜色
              },
              rect: {
                  fill: 'yellow', // 设置背景颜色
                  stroke: 'black', // 设置边框颜色
                  strokeWidth: 1, // 设置边框宽度
              },
            },
          },
        ],
        attrs: {
          // line 是选择器名称，选中的边的 path 元素
          line: {
            stroke: 'red',
            strokeWidth: 2,
          },
        },
      },
    ],
  }

  graph.fromJSON(data)
})
</script>

<template>
  <div ref="containerRef" id="container"></div>
</template>