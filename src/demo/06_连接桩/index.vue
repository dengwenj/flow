<script setup lang="ts">
import { Graph, Path } from '@antv/x6';
import { onMounted, ref } from 'vue';

const containerRef = ref()

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
  },
  ports: {
    groups: {
      top: {
        position: 'top',
        attrs: {
          circle: {
            magnet: true,
            stroke: '#8f8f8f',
            r: 5,
          },
        },
      },
      bottom: {
        position: 'bottom',
        attrs: {
          circle: {
            magnet: true,
            stroke: '#8f8f8f',
            r: 5,
          },
        },
      },
    },
  },
});

// 连接器
Graph.registerConnector(
  'mindmap',
  (sourcePoint, targetPoint, options, args) => {
    const hgap = Math.abs(targetPoint.x - sourcePoint.x)
    const path = new Path()

    path.appendSegment(
      Path.createSegment('M', sourcePoint.x, sourcePoint.y),
    )

    path.appendSegment(
      Path.createSegment('L', sourcePoint.x + 10, sourcePoint.y),
    )
    // 水平三阶贝塞尔曲线
    path.appendSegment(
      Path.createSegment(
        'C',
        sourcePoint.x < targetPoint.x
          ? sourcePoint.x + hgap / 2
          : sourcePoint.x - hgap / 2,
        sourcePoint.y,
        sourcePoint.x < targetPoint.x
          ? targetPoint.x - hgap / 2 - 30
          : targetPoint.x + hgap / 2 - 30,
        targetPoint.y,
        targetPoint.x - 20,
        targetPoint.y,
      ),
    )

    // 最后一根
    path.appendSegment(
      Path.createSegment('L', targetPoint.x, targetPoint.y),
    )

    return path.serialize()
  },
  true,
)






// Graph.registerConnector('mindmap', (sourcePoint, targetPoint) => {
//   const { x: sx, y: sy } = sourcePoint;
//   const { x: tx, y: ty } = targetPoint;
//   const midX = (sx + tx) / 2;
//   return `M ${sx} ${sy} C ${midX} ${sy}, ${midX} ${ty}, ${tx} ${ty}`;
// });

onMounted(() => {
  const graph = new Graph({
    container: containerRef.value,
    width: 800,
    height: 500,
    background: {
      color: '#f1f1f1'
    }
  })

  // 创建一个源节点
    const sourceNode = graph.addNode({
      x: 100,
      y: 300,
      width: 80,
      height: 40,
      label: 'Source',
    });

    // 创建多个目标节点
    const targetNodes = [];
    const targetCount = 4;
    const ySpacing = 100;
    const startY = 100;
    for (let i = 0; i < targetCount; i++) {
      const targetNode = graph.addNode({
        x: 300,
        y: startY + i * ySpacing,
        width: 80,
        height: 40,
        label: `Target ${i + 1}`,
        // port默认不可见
        ports: {
          groups: {
            top: {
              position: 'left',
              attrs: {
                circle: {
                  magnet: true,
                  stroke: '#8f8f8f',
                  r: 0,
                  visibility: 'hidden'
                },
              },
            },
          },
          items: [
            {
              id: 'port_3',
              group: 'top',
            },
          ],
        },
      });
      targetNodes.push(targetNode);
    }

    // 从源节点连接到每个目标节点
    targetNodes.forEach((targetNode, index) => {
      graph.addEdge({
        // source: sourceNode,
        // target: targetNode,
        source: { cell: sourceNode },
        target: { cell: targetNode, port: 'port_3' },
        connector: {
          name: 'mindmap',
          args: {
            radius: 20
          }
        },
        router: {
          name: 'orth',
          args: {
            padding: 10,
            // 根据节点顺序调整路由偏移
            // offset: {
            //   x: index % 2 === 0? -20 : 20,
            //   y: 0
            // }
          }
        },
        attrs: {
          line: {
            stroke: '#31d0c6',
            strokeWidth: 1.5,
            targetMarker: 'block', // 实心箭头
          }
        }
      });
    });
  // const source = graph.addNode({
  //   id: 'node1',
  //   shape: 'custom-node',
  //   x: 40,
  //   y: 40,
  //   label: '节点1',
  //   ports: {
  //     items: [
  //       {
  //         id: 'port_1',
  //         group: 'bottom',
  //       },
  //       {
  //         id: 'port_2',
  //         group: 'bottom',
  //       },
  //     ],
  //   },
  // })

  // const target = graph.addNode({
  //   id: 'node2',
  //   shape: 'custom-node',
  //   x: 200,
  //   y: 40,
  //   label: '节点2',
  //   ports: {
  //     items: [
  //       {
  //         id: 'port_3',
  //         group: 'top',
  //       },
  //       {
  //         id: 'port_4',
  //         group: 'top',
  //       },
  //     ],
  //   },
  // })

  // const target2 = graph.addNode({
  //   id: 'node3',
  //   shape: 'custom-node',
  //   x: 200,
  //   y: 200,
  //   label: '节点3',
  // })

  // const edge = graph.addEdge({
  //   // source: { cell: source, port: 'port_2' },
  //   // target: { cell: target, port: 'port_3' },
  //   source,
  //   target,
  //   fill: '#961ef4',
  //   // 路径点。边从起始点开始，按顺序经过路径点，最后到达终止点
  //   // vertices: [
  //   //   { x: 100, y: 200 },
  //   //   { x: 200, y: 120 },
  //   // ],
  //   // 如果没有 args 参数，可以简写为 router: 'orth'
  //   router: {
  //     name: 'orth',
  //     args: {},
  //   },
  //   // 连接器 connector 将路由 router 返回的点加工成渲染边所需要的 pathData。例如，rounded 连接器将连线之间的倒角处理为圆弧倒角。
  //   connector: 'rounded',
  //   // 设置边的属性
  //   attrs: {
  //     line: {
  //       targetMarker: 'block', // 实心箭头
  //       stroke: '#971df6', // 设置边的颜色为红色
  //       strokeWidth: 1.5,
  //     }
  //   },
  // })

  // setTimeout(() => {
  //   const edge = graph.addEdge({
  //     source,
  //     target: target2,
  //     fill: '#961ef4',
  //     // 路径点。边从起始点开始，按顺序经过路径点，最后到达终止点
  //     // vertices: [
  //     //   { x: 100, y: 200 },
  //     //   { x: 200, y: 120 },
  //     // ],
  //     // 如果没有 args 参数，可以简写为 router: 'orth'
  //     router: {
  //       name: 'orth',
  //       args: {},
  //     },
  //     // 连接器 connector 将路由 router 返回的点加工成渲染边所需要的 pathData。例如，rounded 连接器将连线之间的倒角处理为圆弧倒角。
  //     connector: 'rounded',
  //     // 设置边的属性
  //     attrs: {
  //       line: {
  //         targetMarker: 'block', // 实心箭头
  //         stroke: '#971df6', // 设置边的颜色为红色
  //         strokeWidth: 1.5,
  //       }
  //     },
  //     // labels: [
  //     //   {
  //     //     position: {
  //     //       distance: 0.4, // 边长度的比例，0 表示起点，1 表示终点
  //     //       offset: { x: 20, y: 16 }, // 相对于默认位置的偏移量
  //     //     },
  //     //     attrs: {
  //     //       text: {
  //     //         text: 'Edge Label',
  //     //         fill: 'red', // 设置文字颜色
  //     //       },
  //     //       rect: {
  //     //           fill: 'yellow', // 设置背景颜色
  //     //           stroke: 'black', // 设置边框颜色
  //     //           strokeWidth: 1, // 设置边框宽度
  //     //       },
  //     //     },
  //     //   },
  //     // ],
  //   })
  // }, 2000);
})
</script>

<template>
  <div id="container" ref="containerRef"></div>
</template>