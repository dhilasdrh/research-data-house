<template>
  <div class="relationship-node">
    <div class="relationship-node-label">{{ props.data.label }}</div>
    <Handle type="source" :position="Position.Top" />
    <Handle type="source" :position="Position.Bottom" />
    <Handle type="source" :position="Position.Left" />
    <Handle type="source" :position="Position.Right" />

    <Handle type="target" :position="Position.Top" id="top" />
    <Handle type="target" :position="Position.Bottom" id="bottom" />
    <Handle type="target" :position="Position.Left" id="left" />
    <Handle type="target" :position="Position.Right" id="right" />
  </div>
</template>

<script setup lang="ts">
import { Handle, Position, type NodeProps } from '@vue-flow/core'
const props = defineProps<NodeProps>()
</script>

<style>
.relationship-node {
  width: 80px;
  height: 80px;
  background-color: #fff;
  border: 2px solid #000;
  transform: rotate(45deg);
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.relationship-node-label {
  transform: rotate(-45deg);
  font-size: 12px;
  pointer-events: none;
}
</style>
