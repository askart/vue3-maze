<script setup lang="ts">
import MazeComponent from "@/components/MazeComponent.vue";
</script>

<template>
  <MazeComponent />
</template>

<style lang="scss">
html,
body {
  height: 100%;
  width: 100%;
  font-size: 12px;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  display: flex;
  height: inherit;
  width: inherit;
  justify-content: center;
  align-items: center;
}
</style>
