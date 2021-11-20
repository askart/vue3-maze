<script>
import { inject } from "@vue/runtime-core";

export default {
  setup() {
    const dotSize = inject("dotSize");
    const mazeColor = inject("mazeColor");

    return {
      dotSize,
      mazeColor,
    };
  },
};
</script>

<template>
  <div class="node">
    <slot />
  </div>
</template>

<style scoped lang="scss">
$dot-size: v-bind(dotSize);
$maze-color: v-bind(mazeColor);

.node {
  position: relative;
  display: inline-block;
  height: $dot-size;
  width: $dot-size;
  background-color: $maze-color;
}
</style>
