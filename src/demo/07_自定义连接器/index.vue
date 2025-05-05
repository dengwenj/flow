<script setup lang="ts">
import { Edge, Graph, Node } from '@antv/x6'
import { register } from '@antv/x6-vue-shape';
import { onMounted, ref } from 'vue';
import ContextMenu from '../02_vue_node/component/ContextMenu.vue';

const containerRef = ref()
onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 1500,
    height: 800,
    background: {
      color: '#f1f1f1'
    }
  })

  // 自定义节点
  register({
    shape: 'custom-vue-node',
    width: 50,
    height: 50,
    ports: {
      groups: {
        left: {
          position: 'left',
          attrs: {
            circle: {
              magnet: true,
              stroke: '#8f8f8f',
              r: 2,
              visibility: 'hidden',
            },
          },
        },
        right: {
          position: 'right',
          attrs: {
            circle: {
              magnet: true,
              stroke: '#8f8f8f',
              r: 2,
              visibility: 'hidden',
            },
          }
        },
      },
    },
    component: ContextMenu
  })

  // 自定义连接桩
  Graph.registerConnector('custom-connect', (sourcePort, targetPort) => {
    const diffX = Math.round(targetPort.x) - Math.round(sourcePort.x)
    const diffY = Math.round(targetPort.y) - Math.round(sourcePort.y)

    let d = ''

    const M = `M ${sourcePort.x} ${sourcePort.y} `

    // 横向一半的距离
    const halfDistance = Math.ceil(diffX / 2)
    let radiusX = Math.ceil(halfDistance / 12) * 4
    let radiusY = Math.ceil(halfDistance / 12) * 5
    // 防止 Y半径交叉
    if (Math.abs(diffY) <= radiusY * 2) {
      radiusY = Math.abs(diffY) / 2
    }
    // x 轴移动
    const h = `h ${halfDistance - radiusX} `

    // 横向比值 12 : 4 : 5
    // 第一个值是 圆弧的 x 半径
    // 第二个值是 圆弧的 y 半径
    // 第三个值是 圆弧相对于坐标轴的旋转角度（单位：度）
    // 第四个值是 0 表示绘制小圆弧，1 表示绘制大圆弧
    // 第五个值是 0 为逆时针绘制，1 为顺时针绘制
    // 第六个值是 终点位置 x 坐标
    // 第七个值是 终点位置 y 坐标
    // 旋转角度
    const angle = '0'
    // 小圆弧
    const smallArcs = '0'
    // 逆顺时针
    const counterclockwiseA1 = diffY > 0 ? '1' : '0'
    const counterclockwiseA2 = diffY > 0 ? '0' : '1'
    const endX = radiusX
    const endY = diffY > 0 ? radiusY : -radiusY
    // 圆弧
    const a1 = `a ${radiusX} ${radiusY} ${angle} ${smallArcs} ${counterclockwiseA1} ${endX} ${endY} `
    // y轴移动
    const v = `v ${diffY > 0 ? diffY - radiusY * 2 : diffY + radiusY * 2} `
    // 圆弧
    const a2 = `a ${radiusX} ${radiusY} ${angle} ${smallArcs} ${counterclockwiseA2} ${endX} ${endY} `

    if (diffY === 0) {
      d = M + `h ${diffX}`
    } else {
      d = M + h + a1 + v + a2 + h
    }

    return d
  })

  // 源节点
  const source = graph.addNode({
    id: 'source',
    shape: 'custom-vue-node',
    x: 50,
    y: 300,
    ports: {
      items: [
        {
          id: 'port_1',
          group: 'left',
        },
        {
          id: 'port_2',
          group: 'right',
        },
      ],
    },
  })

  const initPosition = 300
  const targetNodes: Node[] = []
  for (let i = 0; i < 5; i++) {
    // 目标节点
    const target = graph.addNode({
      id: `target${i}`,
      shape: 'custom-vue-node',
      x: initPosition,
      y: (initPosition / 2) * i,
      ports: {
        items: [
          {
            id: 'port_3',
            group: 'left',
          },
          {
            id: 'port_4',
            group: 'right',
          },
        ],
      },
    })

    targetNodes.push(target)
  }

  const colors = ['#e84b65', '#e18338', '#599771', '#355cf3', '#a3218c']
  // 边
  targetNodes.forEach((target, index) => {
    graph.addEdge({
      source: { cell: source, port: 'port_2' },
      target: { cell: target, port: 'port_3' },
      attrs: {
        line: {
          stroke: colors[index],
          strokeWidth: 1.5,
          targetMarker: 'block', // 实心箭头
        },
      },
      router: 'orth',
      connector: {
        name: 'custom-connect'
      }
    })
  })

  setTimeout(() => {
    // 移除节点
    // graph.removeNode(targetNodes[1])
    // 还要删除 数组里面的
  }, 3000);

  // 右键空白处
  graph.on('blank:contextmenu', (e) => {
    graph.addNode({
      shape: 'custom-vue-node',
      x: e.x,
      y: e.y,
      ports: {
        items: [
          {
            id: 'port_3',
            group: 'left',
          },
          {
            id: 'port_4',
            group: 'right',
          },
        ],
      },
    })
    console.log(e, '右键空白处')
  })

  // 右键node节点
  graph.on('node:contextmenu', (e) => {
    console.log(e, '右键节点')
  })


  // 定义距离阈值
  const threshold = 150;
  let edge: Edge
  graph.on('node:changed', (e) => {
    // 灰色才执行

    // 节点的坐标
    const targetPos = e.node.getPosition()
    let nearestSource: any = null;
    let minDistance = Infinity;

    targetNodes
    // 计算目标节点与每个源节点的距离
    targetNodes.forEach(source => {
      const sourcePos = source.getPosition();
      const distance = Math.sqrt(
        Math.pow(targetPos.x - sourcePos.x, 2) + Math.pow(targetPos.y - sourcePos.y, 2)
      );

      if (distance < minDistance) {
        minDistance = distance;
        nearestSource = source;
      }
    });

    // 如果距离小于阈值，添加连接边
    if (minDistance < threshold) {
      const existingEdges = graph.getEdges();
      const hasEdge = existingEdges.some(edge => {
        return edge.getSourceCell() === nearestSource && edge.getTargetCell() === e.node;
      });

      if (!hasEdge) {
        graph.removeEdge(edge)
        edge = graph.addEdge({
          source: { cell: nearestSource, port: 'port_4' },
          target: { cell: e.node, port: 'port_3' },
          attrs: {
            line: {
              stroke: colors[1],
              strokeWidth: 1.5,
              targetMarker: 'block', // 实心箭头
            },
          },
          router: 'orth',
          connector: {
            name: 'custom-connect'
          }
        });
      }
    }
  })
})
</script>

<template>
  <div id="container" ref="containerRef"></div>
</template>