<script lang="ts" setup>
import { computed } from 'vue'
import type { EdgeProps } from '@vue-flow/core'
import { BaseEdge, EdgeLabelRenderer, getSmoothStepPath, Position } from '@vue-flow/core'

const props = defineProps<EdgeProps>()

const path = computed(() =>
  getSmoothStepPath({
    sourceX: props.sourceX,
    sourceY: props.sourceY,
    sourcePosition: props.sourcePosition as Position,
    targetX: props.targetX,
    targetY: props.targetY,
    targetPosition: props.targetPosition as Position,
  }),
)
</script>

<template>
  <BaseEdge
    :id="props.id"
    :path="path[0]"
    marker-start="url(#erd-one)"
    marker-end="url(#erd-many)"
  />

  <EdgeLabelRenderer>
    <div
      :style="{
        pointerEvents: 'all',
        position: 'absolute',
        transform: `translate(-50%, -50%) translate(${path[1]}px,${path[2]}px)`,
      }"
      class="nodrag nopan bg-white text-xs px-1 border rounded"
    >
      {{ props.data.relationship }}
    </div>
  </EdgeLabelRenderer>

  <svg style="height: 0">
    <defs>
      <!-- One-side: double line || -->
      <marker
        id="erd-one"
        markerWidth="10"
        markerHeight="10"
        refX="5"
        refY="5"
        orient="auto-start-reverse"
      >
        <path d="M2,2 L2,8 M5,2 L5,8" stroke="black" stroke-width="1" />
      </marker>

      <!-- Many-side: crow's foot (simplified) -->
      <marker id="erd-many" markerWidth="10" markerHeight="10" refX="8" refY="5" orient="auto">
        <path d="M1,1 L9,5 L1,9" stroke="black" stroke-width="1" fill="none" />
      </marker>
    </defs>
  </svg>
</template>
