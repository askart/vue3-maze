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
    const dotSize = inject("dotSize");
    const cellSize = inject("cellSize");
    const mazeColor = inject("mazeColor");

    return {
      dotSize,
      cellSize,
      mazeColor,
    };
  },
};
</script>

<template>
  <div
    :class="[
      'node__edge',
      `node__edge--${position}`,
      { 'node__edge--hidden': hidden },
    ]"
  />
</template>

<style scoped lang="scss">
$dot-size: v-bind(dotSize);
$cell-size: v-bind(cellSize);
$offset: $dot-size;
$maze-color: v-bind(mazeColor);

.node__edge {
  position: absolute;
  background-color: $maze-color;
  transition: 0.5s all ease-in-out;
  &--top {
    top: 0;
    left: $offset;
    height: $cell-size;
    width: $dot-size;
    transform-origin: 0px 0px;
    transform: rotate(180deg);
  }
  &--top#{&}--hidden {
    height: 0px;
  }
  &--right {
    top: 0;
    left: $offset;
    height: $dot-size;
    width: $cell-size;
  }
  &--right#{&}--hidden {
    width: 0px;
  }
  &--bottom {
    top: $offset;
    left: 0;
    height: $cell-size;
    width: $dot-size;
  }
  &--bottom#{&}--hidden {
    height: 0px;
  }
  &--left {
    top: $offset;
    left: 0;
    height: $dot-size;
    width: $cell-size;
    transform-origin: 0 0;
    transform: rotate(180deg);
  }
  &--left#{&}--hidden {
    width: 0px;
  }
}
</style>
