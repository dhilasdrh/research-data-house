<script lang="ts" setup>
import { ref } from 'vue'
import { Handle, type NodeProps, Position } from '@vue-flow/core'

const props = defineProps<NodeProps>()
const isEditing = ref(false)
const newData = ref(props.data.label)
const emit = defineEmits(['updateData'])

const enableEditing = () => {
  isEditing.value = true
  newData.value = props.data.label
}

const saveLabel = () => {
  if (newData.value !== props.data.label) {
    emit('updateData', props.id, newData.value)
  }
  isEditing.value = false
}

const handleBlur = () => {
  saveLabel()
}

const handleKeydown = (event: KeyboardEvent) => {
  if (event.key === 'Enter') {
    saveLabel()
  } else if (event.key === 'Escape') {
    isEditing.value = false
  }
}
</script>

<template>
  <div class="attribute-node" @dblclick="enableEditing">
    <div class="text-sm text-center">
      <template v-if="isEditing">
        <input
          type="text"
          v-model="newData"
          @blur="handleBlur"
          @keydown="handleKeydown"
          class="border rounded p-1 text-sm"
        />
      </template>
      <template v-else>
        <div class="p-1 text-xs label">{{ props.data.label }}</div>
        <!-- <div class="p-1 text-xs text-gray-600">Primary Key: {{ props.data.primaryKey }}</div>
        <div class="p-1 text-xs text-gray-600">Attributes: {{ props.data.attributes }}</div> -->
      </template>
    </div>
    <Handle type="source" :position="Position.Top" />
    <Handle type="source" :position="Position.Left" />
    <Handle type="source" :position="Position.Right" />
    <Handle type="source" :position="Position.Bottom" />

    <Handle type="target" :position="Position.Top" id="top" />
    <Handle type="target" :position="Position.Left" id="left" />
    <Handle type="target" :position="Position.Right" id="right" />
    <Handle type="target" :position="Position.Bottom" id="bottom" />
  </div>
</template>

<style>
.attribute-node {
  width: 80px;
  height: 80px;
  background-color: #fdf6e3;
  border: 2px solid #333;
  transform: rotate(45deg);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  text-align: center;
  position: relative;
  border-radius: 4px;
}
.label {
  transform: rotate(-45deg);
  width: 100%;
  pointer-events: none;
}
</style>
