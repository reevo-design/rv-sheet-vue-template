<template>
  <div @click="open = true">
   Open RvSheet!
  </div>
  <RvSheet v-model:show="open" is-bg>
    <div style="height: 400px">
      <p style="color: black;">
        Real Reevo, Real Story!
      </p>
    </div>
  </RvSheet>
</template>

<script setup>
import RvSheet from "./components/rv-sheet.vue";
import {ref} from "vue";

const open = ref(false)
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
