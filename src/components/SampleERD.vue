<template>
  <main id="app">
    <div class="h-screen w-screen p-2">
      <div class="space-x-2">
        <!-- <button
          class="border-2 bg-gray-100 py-2 px-3 rounded-lg text-sm"
          type="button"
          @click="handleAddNode"
        >
          Add node
        </button> -->
      </div>

      <VueFlow
        :nodes="nodes"
        :edges="edges"
        :fit-view-on-init="true"
        :min-zoom="0.2"
        :max-zoom="2"
        :default-viewport="{ zoom: 1 }"
        :connection-radius="30"
        :connection-line-type="ConnectionLineType.SmoothStep"
        :pan-on-drag="true"
      >
        <Background pattern-color="#aaa" :gap="8" />
        <template #node-custom="props">
          <CustomNode v-bind="props" @updateData="handleUpdateData" />
        </template>

        <template #node-attribute="props">
          <AttributeNode v-bind="props" />
        </template>

        <template #edge-custom="props">
          <CustomEdge v-bind="props" />
        </template>
      </VueFlow>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ConnectionLineType, useVueFlow, VueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import CustomNode from './CustomNode.vue'
import CustomEdge from './CustomEdge.vue'
import AttributeNode from './AttributeNode.vue'

const { onConnect, getSelectedNodes, getSelectedEdges, setNodes, setEdges } = useVueFlow()

const nodes = ref([
  { id: '1', position: { x: 100, y: 100 }, type: 'custom', data: { label: 'Entity 1' } },
  { id: '2', position: { x: 300, y: 100 }, type: 'custom', data: { label: 'Entity 2' } },
  { id: 'a1', position: { x: 100, y: 250 }, type: 'attribute', data: { label: 'Attribute A' } },
  { id: 'a2', position: { x: 300, y: 250 }, type: 'attribute', data: { label: 'Attribute B' } },
])

const edges = ref([
  {
    id: 'e1-2',
    source: '1',
    target: '2',
    type: 'custom',
    style: { stroke: '#123E6B' },
    sourceHandle: 'right',
    targetHandle: 'left',
  },
  {
    id: 'e1-a1',
    source: '1',
    target: 'a1',
    type: 'custom',
    style: { stroke: '#0b6e4f' },
    sourceHandle: 'bottom',
    targetHandle: 'left',
  },
  {
    id: 'e2-a2',
    source: '2',
    target: 'a2',
    type: 'custom',
    style: { stroke: '#0b6e4f' },
    sourceHandle: 'bottom',
    targetHandle: 'left',
  },
])

// Add a new static node (will show as an alert)
const handleAddNode = () => {
  //
}

const handleUpdateData = (nodeId, newLabel) => {
  const node = nodes.value.find((n) => n.id === nodeId)
  if (node) {
    node.data.label = newLabel
  }
}

const handleRemoveNode = (nodeId) => {
  const nodeIndex = nodes.value.findIndex((node) => node.id === nodeId)
  if (nodeIndex !== -1) {
    nodes.value.splice(nodeIndex, 1)
  }
}

const handleRemoveEdge = (edgeId) => {
  const edgeIndex = edges.value.findIndex((edge) => edge.id === edgeId)
  if (edgeIndex !== -1) {
    edges.value.splice(edgeIndex, 1)
  }
}

const handleKeydown = (event) => {
  if (event.key === 'Delete' || event.key === 'Backspace') {
    const selectedNodes = getSelectedNodes.value
    const selectedEdges = getSelectedEdges.value
    if (selectedNodes.length > 0) {
      handleRemoveNode(selectedNodes[0].id)
    }
    if (selectedEdges.length > 0) {
      handleRemoveEdge(selectedEdges[0].id)
    }
  }
}

onConnect((connection) => {
  const newEdge = {
    id: Date.now().toString(),
    source: connection.source,
    target: connection.target,
    type: 'custom',
    sourceHandle: connection.sourceHandle,
    targetHandle: connection.targetHandle,
    style: { stroke: '#123E6B' },
  }

  edges.value.push(newEdge)
})

onMounted(() => {
  document.addEventListener('keydown', handleKeydown)
})
</script>

<style>
@import '@vue-flow/core/dist/style.css';
@import '@vue-flow/core/dist/theme-default.css';
</style>
