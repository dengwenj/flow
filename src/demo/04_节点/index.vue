<script setup lang="ts">
import { Graph } from '@antv/x6';
import { onMounted, ref } from 'vue';

const containerRef = ref()

// 自定义节点
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
})

onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    background: {
      color: '#f1f1f1'
    }
  })

  // 添加节点
  graph.addNode({
    // 矩形
    shape: 'rect',
    // 圆形
    // shape: 'circle',
    x: 100,
    y: 40,
    width: 200,
    height: 100,
    label: '你好',
    attrs: {
      label: {
        refX: 0.5,
        refY: '100%',
        refY2: 4,
        textAnchor: 'middle',
        textVerticalAnchor: 'top',
      },
      rect: {
        event: 'node:delete'
      }
    },
    // angle: 10
  })

  const node = graph.addNode({
    id: 'cn',
    shape: 'custom-node',
    x: 500,
    y: 10,
    label: '自定义节点',
  })

  graph.on('node:contextmenu', (e) => {
    console.log(e)
    // 修改节点属性
    node.prop('size', { width: Math.random() * 100, height: Math.random() * 100 })
    node.attr('body/fill', 'red')
  })

  graph.on('node:delete', ({ e }: any) => {
    // e.stopPropagation()
    console.log(e)
    console.log("delete")
  })
})
</script>

<template>
  <div id="container" ref="containerRef"></div>
</template>