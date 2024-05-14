<template>
  <codemirror class="custom-codemirror"
              v-model="code"
              placeholder="Code goes here..."
              :autofocus="true"
              :indent-with-tab="true"
              :tabSize="2"
              :height="height"
              :extensions="extensions"
              @ready="log('ready', $event)"
              @change="log('change', $event)"
              @focus="log('focus', $event)"
              @blur="log('blur', $event)"
  />
</template>

<script setup>
import { Codemirror } from "vue-codemirror";
import { javascript } from "@codemirror/lang-javascript";
import { oneDark } from "@codemirror/theme-one-dark";
import { ref } from "vue";

const code = ref(`console.log('Hello, world!')`);
const extensions = [javascript(), oneDark];
const height = '500px'; // 你可以根据需要设置高度

const log = console.log;
</script>

<style scoped>
.custom-codemirror {
  height: 500px; /* 调整高度为你需要的值 */
}
</style>