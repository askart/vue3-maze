<script lang="ts">
import { defineComponent } from "vue";
import { inject } from "@vue/runtime-core";

export default defineComponent({
  props: {
    position: {
      validator(value: string) {
        return ["top", "right", "bottom", "left"].includes(value);
      },
    },
    visible: {
      type: Boolean,
      default: false,
    },
  },
  setup() {
    const verticalCellsCount = inject("verticalCellsCount");
    const horizontalCellsCount = inject("horizontalCellsCount");
    const dotSize = inject("dotSize");
    const cellSize = inject("cellSize");
    const mazeColor = inject("mazeColor");

    return {
      verticalCellsCount,
      horizontalCellsCount,
      dotSize,
      cellSize,
      mazeColor,
    };
  },
});
</script>

<template>
  <div
    :class="['border', `border--${position}`, { 'border--visible': visible }]"
  />
</template>

<style scoped lang="scss">
$vertical-cells-count: v-bind(verticalCellsCount);
$horizontal-cells-count: v-bind(horizontalCellsCount);
$dot-size: v-bind(dotSize);
$cell-size: v-bind(cellSize);
$border-height: calc(
  calc($cell-size * $vertical-cells-count) +
    calc($dot-size * ($vertical-cells-count - 1))
);
$border-width: calc(
  calc($cell-size * ($horizontal-cells-count - 1)) +
    calc($dot-size * ($horizontal-cells-count - 1))
);
$maze-color: v-bind(mazeColor);

.border {
  position: absolute;
  height: $dot-size;
  width: $dot-size;
  background-color: $maze-color;
  transition: 0.5s all ease-in-out;
  &--top {
    top: 0;
    right: 0;
  }
  &--top#{&}--visible {
    width: $border-width;
  }
  &--right {
    top: 0;
    right: 0;
  }
  &--right#{&}--visible {
    height: $border-height;
  }
  &--bottom {
    bottom: 0;
    left: 0;
  }
  &--bottom#{&}--visible {
    width: $border-width;
  }
  &--left {
    bottom: 0;
    left: 0;
  }
  &--left#{&}--visible {
    height: $border-height;
  }
}
</style>
