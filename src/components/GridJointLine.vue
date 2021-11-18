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
    const length = inject("length");
    const mazeColor = inject("mazeColor");

    return {
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
      'joint-line',
      `joint-line--${position}`,
      { 'joint-line--hidden': hidden },
    ]"
  />
</template>

<style scoped lang="scss">
$dot-size: v-bind(dotSize);
$length: v-bind(length);
$offset: $dot-size;
$mazeColor: v-bind(mazeColor);

.joint-line {
  position: absolute;
  background-color: $mazeColor;
  transition: 0.5s all ease-in-out;
  &--top {
    top: 0;
    left: $offset;
    height: $length;
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
    width: $length;
  }
  &--right#{&}--hidden {
    width: 0px;
  }
  &--bottom {
    top: $offset;
    left: 0;
    height: $length;
    width: $dot-size;
  }
  &--bottom#{&}--hidden {
    height: 0px;
  }
  &--left {
    top: $offset;
    left: 0;
    height: $dot-size;
    width: $length;
    transform-origin: 0 0;
    transform: rotate(180deg);
  }
  &--left#{&}--hidden {
    width: 0px;
  }
}
</style>
