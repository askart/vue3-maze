<script>
import { computed, reactive } from "@vue/reactivity";
import { onBeforeUnmount } from "@vue/runtime-core";
import Grid from "./Grid.vue";

export default {
  components: {
    Grid,
  },
  setup() {
    const DX = { RIGHT: 1, LEFT: -1, TOP: 0, BOTTOM: 0 };
    const DY = { RIGHT: 0, LEFT: 0, TOP: -1, BOTTOM: 1 };
    const VALUE = { TOP: 1, BOTTOM: 2, RIGHT: 4, LEFT: 8 };
    const OPPOSITE = {
      RIGHT: "LEFT",
      LEFT: "RIGHT",
      TOP: "BOTTOM",
      BOTTOM: "TOP",
    };

    const state = reactive({
      height: 10,
      width: 10,
      jointMatrix: [],
      hidden: false,
      dotSizeInPixels: "2px",
    });

    const cellSizeInPixels = computed(
      () => `calc(40px - ${state.dotSizeInPixels})`
    );

    let initMazeTimeout, buildMazeTimeout;

    const initMaze = (height, width) => {
      const jointMatrix = [];
      for (let y = 1; y < height; y++) {
        jointMatrix[y - 1] = [];
        for (let x = 1; x < width; x++) {
          jointMatrix[y - 1][x - 1] = {
            LEFT: false,
            TOP: false,
            RIGHT: false,
            BOTTOM: false,
          };
        }
      }
      state.jointMatrix = jointMatrix;
      initMazeTimeout = setTimeout(() => {
        buildMaze(height, width);
        state.hidden = true;
      }, 1000);
    };

    const buildMaze = (height, width) => {
      const grid = makePath(height, width);
      drawMaze(grid, height, width);
      buildMazeTimeout = setTimeout(() => {
        buildMaze(height, width);
      }, 5000);
    };

    onBeforeUnmount(() => {
      clearTimeout(initMazeTimeout);
      initMazeTimeout = null;
      clearTimeout(buildMazeTimeout);
      buildMazeTimeout = null;
    });

    const makePath = (height, width) => {
      const grid = makeGrid(height, width, 0);
      return makePathDepth(0, 0, grid, height, width);
    };

    const makeGrid = (height, width, val) => {
      return Array.from(Array(height), () => new Array(width).fill(val));
    };

    const makePathDepth = (cx, cy, grid, height, width) => {
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
          makePathDepth(nx, ny, grid, height, width);
        }
      });
      return grid;
    };

    const getDirections = () => {
      return shuffle(["TOP", "BOTTOM", "RIGHT", "LEFT"]);
    };

    const shuffle = (array) => {
      for (let i = array.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    };

    const drawMaze = (grid, height, width) => {
      const jointMatrix = [];
      for (let y = 1; y < height; y++) {
        jointMatrix[y - 1] = [];
        for (let x = 1; x < width; x++) {
          const joint = {};
          joint.LEFT = (grid[y - 1][x - 1] & VALUE["BOTTOM"]) == 0;
          joint.TOP = (grid[y - 1][x - 1] & VALUE["RIGHT"]) == 0;
          joint.RIGHT = (grid[y][x] & VALUE["TOP"]) == 0;
          joint.BOTTOM = (grid[y][x] & VALUE["LEFT"]) == 0;
          jointMatrix[y - 1][x - 1] = joint;
        }
      }
      state.jointMatrix = jointMatrix;
    };

    initMaze(state.height, state.width);

    return {
      state,
      cellSizeInPixels,
    };
  },
};
</script>

<template>
  <Grid
    :height="state.height"
    :width="state.width"
    :joint-matrix="state.jointMatrix"
    :hidden="state.hidden"
    :dot-size-in-pixels="state.dotSizeInPixels"
    :cell-size-in-pixels="cellSizeInPixels"
  />
</template>
