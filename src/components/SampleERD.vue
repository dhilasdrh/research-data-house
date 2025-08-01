<template>
  <div class="h-screen w-[1000px] p-2">
    <div class="space-x-2">
      <button
        class="border-2 bg-gray-100 py-2 px-3 rounded-lg text-sm"
        type="button"
        @click="handleAddNode"
      >
        Add Table
      </button>
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
      <template #node-table="props">
        <TableNode v-bind="props" />
      </template>
      <template #edge-custom="props">
        <CustomEdge v-bind="props" />
      </template>
    </VueFlow>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ConnectionLineType, useVueFlow, VueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'

import CustomEdge from './CustomEdge.vue'
import TableNode from './TableNode.vue'

const { onConnect, getSelectedNodes, getSelectedEdges } = useVueFlow()

const nodes = ref([
  {
    id: 'students',
    type: 'table',
    position: { x: 100, y: 200 },
    data: {
      name: 'students',
      columns: [
        { name: 'id', type: 'INT', isPrimary: true },
        { name: 'name', type: 'VARCHAR(100)' },
        { name: 'email', type: 'VARCHAR(100)' },
      ],
    },
  },
  {
    id: 'courses',
    type: 'table',
    position: { x: 500, y: 200 },
    data: {
      name: 'courses',
      columns: [
        { name: 'id', type: 'INT', isPrimary: true },
        { name: 'title', type: 'VARCHAR(255)' },
      ],
    },
  },
  {
    id: 'enrollments',
    type: 'table',
    position: { x: 300, y: 350 },
    data: {
      name: 'enrollments',
      columns: [
        { name: 'student_id', type: 'INT', isPrimary: true },
        { name: 'course_id', type: 'INT', isPrimary: true },
        { name: 'enrolled_at', type: 'DATE' },
      ],
    },
  },
])

const edges = ref([
  {
    id: 'e-enrollment-student',
    source: 'students',
    target: 'enrollments',
    type: 'custom',
    sourceHandle: 'source-right', // student exports from right
    targetHandle: 'target-left', // enrollment receives from left
    data: { relationship: '1:N' },
  },
  {
    id: 'e-enrollment-course',
    source: 'courses',
    target: 'enrollments',
    type: 'custom',
    sourceHandle: 'source-left', // course exports from left
    targetHandle: 'target-right', // enrollment receives from right
    data: { relationship: '1:N' },
  },
])

let tableCount = 1

const handleAddNode = () => {
  const newNode = {
    id: `table-${Date.now()}`,
    type: 'table',
    position: {
      x: 100 + Math.random() * 300,
      y: 100 + Math.random() * 300,
    },
    data: {
      name: `new_table_${tableCount}`,
      columns: [
        { name: 'id', type: 'INT', isPrimary: true },
        { name: 'created_at', type: 'TIMESTAMP' },
      ],
    },
  }

  nodes.value.push(newNode)
  tableCount++
}

const handleRemoveNode = (nodeId) => {
  const nodeIndex = nodes.value.findIndex((node) => node.id === nodeId)
  if (nodeIndex !== -1) nodes.value.splice(nodeIndex, 1)
}

const handleRemoveEdge = (edgeId) => {
  const edgeIndex = edges.value.findIndex((edge) => edge.id === edgeId)
  if (edgeIndex !== -1) edges.value.splice(edgeIndex, 1)
}

const handleKeydown = (event) => {
  if (event.key === 'Delete' || event.key === 'Backspace') {
    const selectedNodes = getSelectedNodes.value
    const selectedEdges = getSelectedEdges.value
    if (selectedNodes.length > 0) handleRemoveNode(selectedNodes[0].id)
    if (selectedEdges.length > 0) handleRemoveEdge(selectedEdges[0].id)
  }
}

onConnect((connection, edge) => {
  const newEdge = {
    id: `edge-${Date.now()}`,
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
