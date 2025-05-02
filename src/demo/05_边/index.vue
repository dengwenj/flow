<script setup lang="ts">
import { Graph } from '@antv/x6';
import { onMounted, ref } from 'vue';

const containerRef = ref();

Graph.registerNode('custom-node', {
  inherit: 'circle',
  width: 100, 
  height: 40,
  // 相当于 html
  markup: [
    {
      tagName: 'circle', // 标签名称
      selector: 'body', // 选择器
    },
    {
      tagName: 'image',
      selector: 'img',
    },
    {
      tagName: 'text',
      selector: 'label',
    },
  ],
  // attrs 相当于 css
  attrs: {
    body: {
      stroke: '#6620f5',
      strokeWidth: 1,
      // 设置边框为虚线，这里的[10, 5]表示虚线的长度为10，间隔为5
      strokeDasharray: [2, 2] as any,
      fill: '#fff'
    },
    img: {
      width: 40,
      height: 40,
      x: 30,
      'xlink:href': 'https://gw.alipayobjects.com/os/s/prod/antv/assets/image/logo-with-text-73b8a.svg'
    },
    label: {
      refX: 0.5,
      refY: '100%',
      refY2: 4,
      textAnchor: 'middle',
      textVerticalAnchor: 'top',
    }
  }
});

onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    background: {
      color: '#f1f1f1'
    }
  })

  const source = graph.addNode({
    id: 'source',
    shape: 'custom-node',
    x: 40,
    y: 40,
    label: '节点1'
  })

  const target = graph.addNode({
    id: 'target',
    shape: 'custom-node',
    x: 300,
    y: 220,
    label: '节点2'
  })

  graph.addEdge({
    source,
    target,
    fill: '#961ef4',
    // 路径点。边从起始点开始，按顺序经过路径点，最后到达终止点
    // vertices: [
    //   { x: 100, y: 200 },
    //   { x: 200, y: 120 },
    // ],
    // 如果没有 args 参数，可以简写为 router: 'orth'
    router: {
      name: 'orth',
      args: {},
    },
    // 连接器 connector 将路由 router 返回的点加工成渲染边所需要的 pathData。例如，rounded 连接器将连线之间的倒角处理为圆弧倒角。
    connector: 'rounded',
    // 设置边的属性
    attrs: {
      line: {
        targetMarker: 'block', // 实心箭头
        stroke: '#971df6', // 设置边的颜色为红色
        strokeWidth: 1.5,
      }
    },
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
  })
})
</script>

<template>
  <div id="container" ref="containerRef"></div>
</template>