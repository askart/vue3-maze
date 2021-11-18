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
  <div class="grid-joint">
    <slot></slot>
  </div>
</template>

<style scoped lang="scss">
$dot-size: v-bind(dotSize);
$mazeColor: v-bind(mazeColor);

.grid-joint {
  position: relative;
  display: inline-block;
  height: $dot-size;
  width: $dot-size;
  background-color: $mazeColor;
}
</style>
