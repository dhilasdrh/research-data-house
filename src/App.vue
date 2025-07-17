<script setup lang="ts">
import { ref } from 'vue'
import SampleCodeMirror from './components/SampleCodeMirror.vue'
import SampleERD2 from './components/SampleERD2.vue'
import SampleMonacoEditor from './components/SampleMonacoEditor.vue'

const activeView = ref<'monaco' | 'codemirror' | 'erd'>('monaco')
</script>

<template>
  <div class="p-4 space-y-4">
    <!-- Switcher Buttons -->
    <div class="space-x-2">
      <button
        class="px-3 py-1 border rounded bg-gray-200 hover:bg-gray-300"
        @click="activeView = 'monaco'"
      >
        Monaco Editor
      </button>
      <button
        class="px-3 py-1 border rounded bg-gray-200 hover:bg-gray-300"
        @click="activeView = 'codemirror'"
      >
        CodeMirror Editor
      </button>
      <button
        class="px-3 py-1 border rounded bg-gray-200 hover:bg-gray-300"
        @click="activeView = 'erd'"
      >
        Sample ERD (VueFlow)
      </button>
    </div>

    <SampleMonacoEditor v-if="activeView === 'monaco'" />
    <SampleCodeMirror v-if="activeView === 'codemirror'" />
    <SampleERD2 v-if="activeView === 'erd'" />
  </div>
</template>
