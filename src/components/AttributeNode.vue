<template>
  <div class="attribute-node">
    <div class="label">{{ props.data.label }}</div>
    <Handle type="target" :position="Position.Top" />
    <Handle type="target" :position="Position.Bottom" />
    <Handle type="target" :position="Position.Left" />
    <Handle type="target" :position="Position.Right" />
  </div>
</template>

<script setup lang="ts">
import { Handle, Position, type NodeProps } from '@vue-flow/core'
const props = defineProps<NodeProps>()
</script>

<style>
.attribute-node {
  width: 100px;
  height: 60px;
  aspect-ratio: 2 / 1;
  background-color: #fdf6e3;
  border: 2px solid #333;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  position: relative;
  box-sizing: border-box;
}

.label {
  pointer-events: none;
}
</style>
