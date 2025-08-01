<script setup lang="ts">
import { ref, shallowRef, nextTick } from 'vue'
import type * as monaco from 'monaco-editor'

const scriptValue = ref('SELECT * FROM users;')
const editorTheme = ref<'vs' | 'vs-dark'>('vs-dark')
const editorRef = shallowRef<monaco.editor.IStandaloneCodeEditor>()

const MONACO_EDITOR_OPTIONS = {
  lineHeight: 20,
  fontSize: 12,
  lineDecorationsWidth: 5,
  renderIndentGuides: false,
  renderLineHighlight: 'none',
  fontFamily: 'Source Code Pro, monospace',
  overviewRulerBorder: false,
  automaticLayout: true,
  formatOnType: true,
  formatOnPaste: true,
  disableLayerHinting: true,
  minimap: { enabled: false },
  scrollBeyondLastLine: false,
  quickSuggestions: { other: true, comments: false, strings: true },
  scrollbar: {
    horizontal: 'auto',
    vertical: 'auto',
    horizontalScrollbarSize: 4,
    horizontalSliderSize: 4,
    verticalScrollbarSize: 4,
    verticalSliderSize: 4,
  },
}

let sqlRegistered = false

function registerSql(monaco: typeof import('monaco-editor')) {
  if (sqlRegistered) return
  sqlRegistered = true
  monaco.languages.register({ id: 'sql' })
  monaco.languages.setMonarchTokensProvider('sql', {
    tokenizer: {
      root: [
        [
          /\b(SELECT|FROM|WHERE|INSERT|INTO|VALUES|UPDATE|SET|DELETE|JOIN|LEFT|RIGHT|INNER|OUTER|FULL|ON|AS|AND|OR|NOT|GROUP BY|ORDER BY|HAVING|LIMIT|OFFSET|CREATE|ALTER|DROP|TRUNCATE|UNION|ALL|DISTINCT)\b/i,
          'keyword',
        ],
        [/\b(TRUE|FALSE|NULL)\b/i, 'constant'],
        [/'[^']*'/, 'string'],
        [/".*?"/, 'string'],
        [/\b\d+\b/, 'number'],
      ],
    },
  })

  monaco.languages.registerCompletionItemProvider('sql', {
    triggerCharacters: [' ', '.', '\t'],
    provideCompletionItems: (model, position) => {
      const wordInfo = model.getWordAtPosition(position) || {
        word: '',
        startColumn: position.column,
        endColumn: position.column,
      }

      const range = {
        startLineNumber: position.lineNumber,
        endLineNumber: position.lineNumber,
        startColumn: wordInfo.startColumn,
        endColumn: wordInfo.endColumn,
      }

      const suggestions: monaco.languages.CompletionItem[] = [
        // Core SQL commands
        {
          label: 'SELECT',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'SELECT * FROM ',
          range,
        },
        {
          label: 'INSERT',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'INSERT INTO table_name (...) VALUES (...);',
          range,
        },
        {
          label: 'UPDATE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'UPDATE table_name SET column = value WHERE condition;',
          range,
        },
        {
          label: 'DELETE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'DELETE FROM table_name WHERE condition;',
          range,
        },
        {
          label: 'CREATE TABLE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText:
            'CREATE TABLE table_name (\n  id INT PRIMARY KEY,\n  column_name VARCHAR(255)\n);',
          range,
        },
        {
          label: 'ALTER TABLE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'ALTER TABLE table_name ADD column_name datatype;',
          range,
        },
        {
          label: 'DROP TABLE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'DROP TABLE table_name;',
          range,
        },
        {
          label: 'TRUNCATE TABLE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'TRUNCATE TABLE table_name;',
          range,
        },
        {
          label: 'CREATE INDEX',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'CREATE INDEX index_name ON table_name (column_name);',
          range,
        },

        // Joins
        {
          label: 'JOIN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'JOIN table2 ON table1.id = table2.fk_id',
          range,
        },
        {
          label: 'LEFT JOIN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'LEFT JOIN table2 ON table1.id = table2.fk_id',
          range,
        },
        {
          label: 'RIGHT JOIN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'RIGHT JOIN table2 ON table1.id = table2.fk_id',
          range,
        },
        {
          label: 'FULL OUTER JOIN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'FULL OUTER JOIN table2 ON table1.id = table2.fk_id',
          range,
        },

        // Clauses
        {
          label: 'WHERE',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'WHERE condition',
          range,
        },
        {
          label: 'ORDER BY',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'ORDER BY column_name ASC',
          range,
        },
        {
          label: 'GROUP BY',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'GROUP BY column_name',
          range,
        },
        {
          label: 'HAVING',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'HAVING COUNT(column_name) > 1',
          range,
        },
        {
          label: 'LIMIT',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'LIMIT 10',
          range,
        },
        {
          label: 'OFFSET',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'OFFSET 0',
          range,
        },

        // Logical operators
        {
          label: 'AND',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'AND ',
          range,
        },
        {
          label: 'OR',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'OR ',
          range,
        },
        {
          label: 'NOT',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'NOT ',
          range,
        },
        {
          label: 'IN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'IN (...)',
          range,
        },
        {
          label: 'BETWEEN',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'BETWEEN value1 AND value2',
          range,
        },
        {
          label: 'IS NULL',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'IS NULL',
          range,
        },
        {
          label: 'IS NOT NULL',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'IS NOT NULL',
          range,
        },

        // Set operations
        {
          label: 'UNION',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'UNION',
          range,
        },
        {
          label: 'ALL',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'ALL',
          range,
        },
        {
          label: 'ANY',
          kind: monaco.languages.CompletionItemKind.Keyword,
          insertText: 'ANY',
          range,
        },

        // Aggregates & Functions
        {
          label: 'COUNT',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'COUNT(column_name)',
          range,
        },
        {
          label: 'SUM',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'SUM(column_name)',
          range,
        },
        {
          label: 'AVG',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'AVG(column_name)',
          range,
        },
        {
          label: 'MIN',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'MIN(column_name)',
          range,
        },
        {
          label: 'MAX',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'MAX(column_name)',
          range,
        },

        // Date/time
        {
          label: 'NOW()',
          kind: monaco.languages.CompletionItemKind.Function,
          insertText: 'NOW()',
          range,
        },
        {
          label: 'CURRENT_DATE',
          kind: monaco.languages.CompletionItemKind.Constant,
          insertText: 'CURRENT_DATE',
          range,
        },
        {
          label: 'CURRENT_TIME',
          kind: monaco.languages.CompletionItemKind.Constant,
          insertText: 'CURRENT_TIME',
          range,
        },

        // Constants
        {
          label: 'TRUE',
          kind: monaco.languages.CompletionItemKind.Constant,
          insertText: 'TRUE',
          range,
        },
        {
          label: 'FALSE',
          kind: monaco.languages.CompletionItemKind.Constant,
          insertText: 'FALSE',
          range,
        },
        {
          label: 'NULL',
          kind: monaco.languages.CompletionItemKind.Constant,
          insertText: 'NULL',
          range,
        },
      ]

      return { suggestions }
    },
  })
}

function handleBeforeMount(monaco: typeof import('monaco-editor')) {
  registerSql(monaco)
}

function handleEditorMount(editor: monaco.editor.IStandaloneCodeEditor) {
  editorRef.value = editor
  nextTick(() => editor.layout())
}

function handleUpdateValue(value: string) {
  scriptValue.value = value
}
</script>

<template>
  <div class="flex flex-col space-y-4 w-full h-full">
    <h2 class="text-lg font-bold">Monaco Editor</h2>
    <div class="flex space-x-4 items-center">
      <label>
        <input type="radio" value="vs-dark" v-model="editorTheme" />
        Dark
      </label>
      <label>
        <input type="radio" value="vs" v-model="editorTheme" />
        Light
      </label>
    </div>

    <div class="w-full h-[60vh] rounded-lg">
      <vue-monaco-editor
        :value="scriptValue"
        :language="'sql'"
        :theme="editorTheme"
        width="800px"
        height="100%"
        :options="MONACO_EDITOR_OPTIONS"
        @beforeMount="handleBeforeMount"
        @mount="handleEditorMount"
        @change="handleUpdateValue"
      />
    </div>
  </div>
</template>

<style>
.monaco-editor .overflow-guard {
  border: 1px solid #d3d3d3;
}

.margin-view-overlays {
  background-color: #f8f9fa;
}

.monaco-editor.vs-dark .margin-view-overlays {
  background-color: #1e1e1e !important;
}

.cm-editor-wrapper.dark .cm-content {
  background-color: #1e1e1e !important;
}

.cm-editor-wrapper.light .cm-content {
  color: #1e1e1e !important;
}
</style>
