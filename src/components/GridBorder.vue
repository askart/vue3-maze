<script>
import { inject } from "@vue/runtime-core";

export default {
  props: {
    position: {
      validator(value) {
        return ["top", "right", "bottom", "left"].includes(value);
      },
    },
    hidden: {
      type: Boolean,
      default: false,
    },
  },
  setup() {
    const verticalCellsCount = inject("verticalCellsCount");
    const horizontalCellsCount = inject("horizontalCellsCount");
    const dotSize = inject("dotSize");
    const length = inject("length");
    const mazeColor = inject("mazeColor");

    return {
      verticalCellsCount,
      horizontalCellsCount,
      dotSize,
      length,
      mazeColor,
    };
  },
};
</script>

<template>
  <div
    :class="[
      'grid-border',
      `grid-border--${position}`,
      { 'grid-border--hidden': hidden },
    ]"
  />
</template>

<style scoped lang="scss">
$vertical-cells-count: v-bind(verticalCellsCount);
$horizontal-cells-count: v-bind(horizontalCellsCount);
$dot-size: v-bind(dotSize);
$length: v-bind(length);
$border-height: calc(
  ($length * $vertical-cells-count) + ($dot-size * ($vertical-cells-count - 1))
);
$border-width: calc(
  ($length * ($horizontal-cells-count - 1)) +
    ($dot-size * ($horizontal-cells-count - 1))
);
$mazeColor: v-bind(mazeColor);

.grid-border {
  position: absolute;
  height: $dot-size;
  width: $dot-size;
  background-color: $mazeColor;
  transition: 0.5s all ease-in-out;
  &--top {
    top: 0;
    right: 0;
  }
  &--top#{&}--hidden {
    width: $border-width;
  }
  &--right {
    top: 0;
    right: 0;
  }
  &--right#{&}--hidden {
    height: $border-height;
  }
  &--bottom {
    bottom: 0;
    left: 0;
  }
  &--bottom#{&}--hidden {
    width: $border-width;
  }
  &--left {
    bottom: 0;
    left: 0;
  }
  &--left#{&}--hidden {
    height: $border-height;
  }
}
</style>
