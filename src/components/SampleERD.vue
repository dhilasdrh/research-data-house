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
        :connection-line-type="ConnectionLineType.Straight"
        :pan-on-drag="true"
      >
        <Background pattern-color="#aaa" :gap="8" />
        <template #node-entity="props">
          <EntityNode v-bind="props" @updateData="handleUpdateData" />
        </template>

        <template #node-attribute="props">
          <AttributeNode v-bind="props" />
        </template>

        <template #node-relationship="props">
          <RelationshipNode v-bind="props" />
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
import CustomNode from './EntityNode.vue'
import CustomEdge from './CustomEdge.vue'
import AttributeNode from './AttributeNode.vue'
import RelationshipNode from './RelationshipNode.vue'
import EntityNode from './EntityNode.vue'

const { onConnect, getSelectedNodes, getSelectedEdges, setNodes, setEdges } = useVueFlow()

const nodes = ref([
  { id: 'student', position: { x: 100, y: 200 }, type: 'entity', data: { label: 'Student' } },
  { id: 'studentName', position: { x: 10, y: 200 }, type: 'attribute', data: { label: 'Name' } },

  { id: 'course', position: { x: 500, y: 200 }, type: 'entity', data: { label: 'Course' } },
  { id: 'courseName', position: { x: 600, y: 200 }, type: 'attribute', data: { label: 'Title' } },

  { id: 'enrolls', position: { x: 300, y: 300 }, type: 'relationship', data: { label: 'Enrolls' } },
  { id: 'grade', position: { x: 300, y: 400 }, type: 'attribute', data: { label: 'Grade' } },
])

const edges = ref([
  // Entity → Relationship (bottom to top)
  {
    id: 'e-student-enrolls',
    source: 'student',
    target: 'enrolls',
    type: 'custom',
    sourceHandle: 'bottom',
    targetHandle: 'left',
  },
  {
    id: 'e-course-enrolls',
    source: 'course',
    target: 'enrolls',
    type: 'custom',
    sourceHandle: 'bottom',
    targetHandle: 'right',
  },

  // Attributes → Entity (right to left, and left to right)
  {
    id: 'e-studentName',
    source: 'student',
    target: 'studentName',
    type: 'custom',
    sourceHandle: 'left',
    targetHandle: 'right',
  },
  {
    id: 'e-courseName',
    source: 'course',
    target: 'courseName',
    type: 'custom',
    sourceHandle: 'right',
    targetHandle: 'left',
  },

  // Attribute → Relationship (bottom to top)
  {
    id: 'e-grade',
    source: 'enrolls',
    target: 'grade',
    type: 'custom',
    sourceHandle: 'bottom',
    targetHandle: 'top',
  },
])

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
