<script setup lang="ts">
import { Handle, Position, type NodeProps } from '@vue-flow/core'

const props = defineProps<NodeProps>()
const columns = props.data.columns || []
</script>

<template>
  <div class="bg-white border border-gray-200 rounded shadow min-w-[180px]">
    <div class="font-bold text-sm text-center mb-2 bg-gray-100 py-1">{{ props.data.name }}</div>
    <ul class="text-sm px-2">
      <li v-for="col in columns" :key="col.name" class="flex justify-between text-xs py-1">
        <span>{{ col.name }}</span>
        <span>{{ col.type }}</span>
      </li>
    </ul>

    <Handle type="source" :position="Position.Top" id="source-top" />
    <Handle type="source" :position="Position.Right" id="source-right" />
    <Handle type="source" :position="Position.Bottom" id="source-bottom" />
    <Handle type="source" :position="Position.Left" id="source-left" />

    <Handle type="target" :position="Position.Top" id="target-top" />
    <Handle type="target" :position="Position.Right" id="target-right" />
    <Handle type="target" :position="Position.Bottom" id="target-bottom" />
    <Handle type="target" :position="Position.Left" id="target-left" />
  </div>
</template>
