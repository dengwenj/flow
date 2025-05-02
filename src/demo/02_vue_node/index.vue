<script setup lang="ts">
import { Graph, Model } from '@antv/x6'
import { register } from '@antv/x6-vue-shape';
import { onMounted, ref } from 'vue';
import ContextMenu from './component/ContextMenu.vue';

const containerRef = ref()
const count = ref(1)

onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    background: {
      color: '#F2F7FA'
    },
  })

  // 注册自定义节点，给节点加上右键菜单
  register({
    shape: 'custom-vue-node',
    width: 60,
    height: 60,
    component: ContextMenu,
    count: count.value
  })

  const data: Model.FromJSONData = {
    nodes: [
      {
        id: 'node1',
        shape: 'custom-vue-node',
        x: 40,
        y: 40,
        label: '标签'
      }
    ]
  }

  graph.fromJSON(data)

  graph.addNode({
    id: 'node2',
    shape: 'custom-vue-node',
    x: 100,
    y: 100,
    label: 'node2'
  })

  const json = graph.toJSON()
  console.log(json)
})
</script>


<template>
  <div id="container" ref="containerRef"></div>
</template>