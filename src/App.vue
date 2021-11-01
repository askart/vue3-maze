<script setup>
import Maze from "./components/Maze.vue";
</script>

<template>
  <Maze />
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
