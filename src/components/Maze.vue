<script>
import { reactive } from "@vue/reactivity";

export default {
  setup() {
    const height = 10;
    const width = 15;
    const DX = { E: 1, W: -1, N: 0, S: 0 };
    const DY = { E: 0, W: 0, N: -1, S: 1 };
    const VALUE = { N: 1, S: 2, E: 4, W: 8 };
    const OPPOSITE = { E: "W", W: "E", N: "S", S: "N" };

    const state = reactive({
      jointMatrix: [],
      gridAnimation: false,
    });

    const initMaze = () => {
      const jointMatrix = [];
      for (let y = 1; y < height; y++) {
        jointMatrix[y - 1] = [];
        for (let x = 1; x < width; x++) {
          jointMatrix[y - 1][x - 1] = {
            W: false,
            N: false,
            E: false,
            S: false,
          };
        }
      }
      state.jointMatrix = jointMatrix;
      setTimeout(() => {
        buildMaze();
        state.gridAnimation = true;
      }, 1000);
    };

    const buildMaze = () => {
      const grid = makePath();
      drawMaze(grid);
      setTimeout(() => {
        buildMaze();
      }, 5000);
    };

    const makePath = () => {
      const grid = makeGrid(height, width, 0);
      return makePathDepth(0, 0, grid);
    };

    const makeGrid = (height, width, val) => {
      return Array.from(Array(height), () => new Array(width).fill(val));
    };

    const makePathDepth = (cx, cy, grid) => {
      const directions = getDirections();
      directions.forEach((dir) => {
        const nx = cx + DX[dir],
          ny = cy + DY[dir];
        if (
          ny >= 0 &&
          ny < height &&
          nx >= 0 &&
          nx < width &&
          grid[ny][nx] == 0
        ) {
          grid[cy][cx] |= VALUE[dir];
          grid[ny][nx] |= VALUE[OPPOSITE[dir]];
          makePathDepth(nx, ny, grid);
        }
      });
      return grid;
    };

    const getDirections = () => {
      return shuffle(["N", "S", "E", "W"]);
    };

    const shuffle = (array) => {
      for (let i = array.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    };

    const drawMaze = (grid) => {
      const jointMatrix = [];
      for (let y = 1; y < height; y++) {
        jointMatrix[y - 1] = [];
        for (let x = 1; x < width; x++) {
          const joint = {};
          joint.W = (grid[y - 1][x - 1] & VALUE["S"]) == 0;
          joint.N = (grid[y - 1][x - 1] & VALUE["E"]) == 0;
          joint.E = (grid[y][x] & VALUE["N"]) == 0;
          joint.S = (grid[y][x] & VALUE["W"]) == 0;
          jointMatrix[y - 1][x - 1] = joint;
        }
      }
      state.jointMatrix = jointMatrix;
    };

    initMaze();

    return {
      state,
    };
  },
};
</script>

<template>
  <div class="grid">
    <div v-for="(row, y) in state.jointMatrix" :key="y" class="grid__row">
      <div v-for="(joint, x) in row" :key="x" class="grid__joint">
        <div :class="['grid__joint__line--top', { hidden: !joint.N }]" />
        <div :class="['grid__joint__line--right', { hidden: !joint.E }]" />
        <div :class="['grid__joint__line--bottom', { hidden: !joint.S }]" />
        <div :class="['grid__joint__line--left', { hidden: !joint.W }]" />
      </div>
    </div>
    <div
      :class="[
        'grid__border--top',
        { 'grid__border--top--animated': state.gridAnimation },
      ]"
    />
    <div
      :class="[
        'grid__border--right',
        { 'grid__border--right--animated': state.gridAnimation },
      ]"
    />
    <div
      :class="[
        'grid__border--bottom',
        { 'grid__border--bottom--animated': state.gridAnimation },
      ]"
    />
    <div
      :class="[
        'grid__border--left',
        { 'grid__border--left--animated': state.gridAnimation },
      ]"
    />
  </div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$vertical-cells-count: 10;
$horizontal-cells-count: 15;
$thickness: 2px;
$length: 50px - $thickness;
$offset: $thickness;
$top-bottom-border-length: ($length * ($horizontal-cells-count - 1)) +
  ($thickness * ($horizontal-cells-count - 1));
$left-right-border-length: ($length * $vertical-cells-count) +
  ($thickness * ($vertical-cells-count - 1));
$gray-dark: darken(lightgray, 50%);
.grid {
  flex: 0 1 auto;
  position: relative;
  background-color: white;
  &__border {
    &--top {
      position: absolute;
      top: 0;
      right: 0;
      height: $thickness;
      width: $thickness;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &--animated {
        width: $top-bottom-border-length;
      }
    }
    &--right {
      position: absolute;
      top: 0;
      right: 0;
      height: $thickness;
      width: $thickness;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &--animated {
        height: $left-right-border-length;
      }
    }
    &--bottom {
      position: absolute;
      bottom: 0;
      left: 0;
      height: $thickness;
      width: $thickness;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &--animated {
        width: $top-bottom-border-length;
      }
    }
    &--left {
      position: absolute;
      bottom: 0;
      left: 0;
      height: $thickness;
      width: $thickness;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &--animated {
        height: $left-right-border-length;
      }
    }
  }
  &__row {
    display: flex;
  }
  &__row:first-child > &__joint {
    margin-top: $length;
  }
  &__joint {
    display: inline-block;
    position: relative;
    height: $thickness;
    width: $thickness;
    background-color: $gray-dark;
    margin: 0 $length $length 0;
    &:first-child {
      margin-left: $length;
    }
    &__line--top {
      position: absolute;
      top: 0;
      left: $offset;
      height: $length;
      width: $thickness;
      background-color: $gray-dark;
      transform-origin: 0px 0px;
      transform: rotate(180deg);
      transition: 0.5s all ease-in-out;
      &.hidden {
        height: 0px;
      }
    }
    &__line--right {
      position: absolute;
      top: 0;
      left: $offset;
      height: $thickness;
      width: $length;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &.hidden {
        width: 0px;
      }
    }
    &__line--bottom {
      position: absolute;
      top: $offset;
      left: 0;
      height: $length;
      width: $thickness;
      background-color: $gray-dark;
      transition: 0.5s all ease-in-out;
      &.hidden {
        height: 0px;
      }
    }
    &__line--left {
      position: absolute;
      top: $offset;
      left: 0;
      height: $thickness;
      width: $length;
      background-color: $gray-dark;
      transform-origin: 0 0;
      transform: rotate(180deg);
      transition: 0.5s all ease-in-out;
      &.hidden {
        width: 0px;
      }
    }
  }
}
</style>
