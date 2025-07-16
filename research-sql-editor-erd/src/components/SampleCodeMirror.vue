<script setup lang="ts">
import { ref, computed } from 'vue'
import { Codemirror } from 'vue-codemirror'
import { sql } from '@codemirror/lang-sql'
import { oneDark } from '@codemirror/theme-one-dark'
import { autocompletion, CompletionContext } from '@codemirror/autocomplete'

const code = ref('SELECT * FROM users;')
const theme = ref<'dark' | 'light'>('dark')

// Define custom SQL completions
function sqlAutocomplete(context: CompletionContext) {
  const word = context.matchBefore(/\w*/)
  if (!word || (word.from == word.to && !context.explicit)) return null

  const suggestions = [
    { label: 'SELECT', type: 'keyword', apply: 'SELECT * FROM ' },
    { label: 'INSERT', type: 'keyword', apply: 'INSERT INTO table_name (...) VALUES (...);' },
    {
      label: 'UPDATE',
      type: 'keyword',
      apply: 'UPDATE table_name SET column = value WHERE condition;',
    },
    { label: 'DELETE', type: 'keyword', apply: 'DELETE FROM table_name WHERE condition;' },
    {
      label: 'CREATE TABLE',
      type: 'keyword',
      apply: 'CREATE TABLE table_name (\n  id INT PRIMARY KEY,\n  name VARCHAR(255)\n);',
    },
    {
      label: 'ALTER TABLE',
      type: 'keyword',
      apply: 'ALTER TABLE table_name ADD column_name datatype;',
    },
    { label: 'DROP TABLE', type: 'keyword', apply: 'DROP TABLE table_name;' },
    { label: 'TRUNCATE TABLE', type: 'keyword', apply: 'TRUNCATE TABLE table_name;' },
    { label: 'JOIN', type: 'keyword', apply: 'JOIN table2 ON table1.id = table2.fk_id' },
    { label: 'LEFT JOIN', type: 'keyword' },
    { label: 'RIGHT JOIN', type: 'keyword' },
    { label: 'FULL OUTER JOIN', type: 'keyword' },
    { label: 'GROUP BY', type: 'keyword' },
    { label: 'ORDER BY', type: 'keyword', apply: 'ORDER BY column_name ASC' },
    { label: 'HAVING', type: 'keyword' },
    { label: 'LIMIT', type: 'keyword', apply: 'LIMIT 10' },
    { label: 'OFFSET', type: 'keyword', apply: 'OFFSET 0' },
    { label: 'DISTINCT', type: 'keyword' },
    { label: 'AS', type: 'keyword' },
    { label: 'IN', type: 'keyword' },
    { label: 'EXISTS', type: 'keyword' },
    { label: 'NOT', type: 'keyword' },
    { label: 'AND', type: 'keyword' },
    { label: 'OR', type: 'keyword' },
    { label: 'BETWEEN', type: 'keyword' },
    { label: 'IS NULL', type: 'keyword' },
    { label: 'IS NOT NULL', type: 'keyword' },
    { label: 'UNION', type: 'keyword' },
    { label: 'ALL', type: 'keyword' },
    { label: 'ANY', type: 'keyword' },
    { label: 'COUNT', type: 'function', apply: 'COUNT(column_name)' },
    { label: 'SUM', type: 'function', apply: 'SUM(column_name)' },
    { label: 'AVG', type: 'function', apply: 'AVG(column_name)' },
    { label: 'MIN', type: 'function', apply: 'MIN(column_name)' },
    { label: 'MAX', type: 'function', apply: 'MAX(column_name)' },
    { label: 'NOW()', type: 'function' },
    { label: 'CURRENT_DATE', type: 'constant' },
    { label: 'CURRENT_TIME', type: 'constant' },
    { label: 'TRUE', type: 'constant' },
    { label: 'FALSE', type: 'constant' },
    { label: 'NULL', type: 'constant' },
  ]

  return {
    from: word.from,
    options: suggestions,
    validFor: /^\w*$/,
  }
}

const extensions = computed(() => [
  sql(),
  autocompletion({ override: [sqlAutocomplete], activateOnTyping: true }),
  theme.value === 'dark' ? oneDark : [],
])
</script>

<template>
  <div class="space-y-4 mt-5">
    <h2 class="text-lg font-bold">CodeMirror</h2>
    <div class="space-x-4 flex items-center">
      <label><input type="radio" value="dark" v-model="theme" /> Dark</label>
      <label><input type="radio" value="light" v-model="theme" /> Light</label>
    </div>
    <Codemirror
      v-model="code"
      :extensions="extensions"
      placeholder="Write SQL..."
      autofocus
      :indent-with-tab="true"
      :tab-size="2"
    />
  </div>
</template>

<style>
.cm-editor {
  height: 400px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-family: 'Source Code Pro', monospace;
  font-size: 13px;
}

.cm-line {
  text-align: start;
}
</style>
