<script lang="ts">
import { defineComponent } from "vue";
import { computed, reactive } from "@vue/reactivity";
import { onBeforeUnmount, provide } from "@vue/runtime-core";
import MazeNode from "./MazeNode.vue";
import MazeNodeEdge from "./MazeNodeEdge.vue";
import MazeBorder from "./MazeBorder.vue";

type Direction = "RIGHT" | "LEFT" | "TOP" | "BOTTOM";

type Node = {
  LEFT: boolean;
  TOP: boolean;
  RIGHT: boolean;
  BOTTOM: boolean;
};

interface State {
  nodes: Node[][];
  borderVisible: boolean;
}

export default defineComponent({
  components: {
    MazeNode,
    MazeNodeEdge,
    MazeBorder,
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

    const state: State = reactive({
      nodes: [],
      borderVisible: false,
    });

    const height = 10;
    const width = 10;
    const dotSizeInPixels = "2px";
    const cellSizeInPixels = computed(() => `calc(40px - ${dotSizeInPixels})`);

    let initMazeTimeout: number | undefined,
      buildMazeTimeout: number | undefined;

    onBeforeUnmount(() => {
      clearTimeout(initMazeTimeout);
      clearTimeout(buildMazeTimeout);
    });

    const initMaze = (height: number, width: number) => {
      const nodes: Node[][] = [];
      for (let y = 1; y < height; y++) {
        nodes[y - 1] = [];
        for (let x = 1; x < width; x++) {
          nodes[y - 1][x - 1] = {
            LEFT: false,
            TOP: false,
            RIGHT: false,
            BOTTOM: false,
          };
        }
      }

      state.nodes = nodes;

      initMazeTimeout = window.setTimeout(() => {
        buildMaze(height, width);
        state.borderVisible = true;
      }, 1000);
    };

    const buildMaze = (height: number, width: number) => {
      const maze = makeMazePath(height, width);
      drawMaze(maze, height, width);
      buildMazeTimeout = window.setTimeout(() => {
        buildMaze(height, width);
      }, 5000);
    };

    const makeMazePath = (height: number, width: number) => {
      const maze = clearMaze(height, width, 0);
      return makeMazePathDepth(0, 0, maze, height, width);
    };

    const clearMaze = (height: number, width: number, val: number) => {
      return Array.from(Array(height), () => new Array(width).fill(val));
    };

    const makeMazePathDepth = (
      cx: number,
      cy: number,
      maze: number[][],
      height: number,
      width: number
    ) => {
      const directions = getDirections();
      directions.forEach((dir: Direction) => {
        const nx = cx + DX[dir],
          ny = cy + DY[dir];
        if (
          ny >= 0 &&
          ny < height &&
          nx >= 0 &&
          nx < width &&
          maze[ny][nx] == 0
        ) {
          maze[cy][cx] |= VALUE[dir];
          maze[ny][nx] |= VALUE[OPPOSITE[dir] as Direction];
          makeMazePathDepth(nx, ny, maze, height, width);
        }
      });
      return maze;
    };

    const getDirections = () => {
      return shuffle(["TOP", "BOTTOM", "RIGHT", "LEFT"]);
    };

    const shuffle = (array: Direction[]) => {
      for (let i = array.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    };

    const drawMaze = (maze: number[][], height: number, width: number) => {
      const nodes: Node[][] = [];
      for (let y = 1; y < height; y++) {
        nodes[y - 1] = [];
        for (let x = 1; x < width; x++) {
          const node = {
            LEFT: (maze[y - 1][x - 1] & VALUE["BOTTOM"]) == 0,
            TOP: (maze[y - 1][x - 1] & VALUE["RIGHT"]) == 0,
            RIGHT: (maze[y][x] & VALUE["TOP"]) == 0,
            BOTTOM: (maze[y][x] & VALUE["LEFT"]) == 0,
          };

          nodes[y - 1][x - 1] = node;
        }
      }
      state.nodes = nodes;
    };

    provide("verticalCellsCount", height);
    provide("horizontalCellsCount", width);
    provide("dotSize", dotSizeInPixels);
    provide("cellSize", cellSizeInPixels);
    provide("mazeColor", "gray");

    initMaze(height, width);

    return {
      state,
      cellSizeInPixels,
    };
  },
});
</script>

<template>
  <div class="maze">
    <div
      v-for="(row, rowIndex) in state.nodes"
      :key="rowIndex"
      class="maze__row"
    >
      <MazeNode
        v-for="(node, nodeIndex) in row"
        :key="`${rowIndex}_${nodeIndex}`"
        class="maze__node"
      >
        <MazeNodeEdge position="top" :hidden="!node.TOP" />
        <MazeNodeEdge position="right" :hidden="!node.RIGHT" />
        <MazeNodeEdge position="bottom" :hidden="!node.BOTTOM" />
        <MazeNodeEdge position="left" :hidden="!node.LEFT" />
      </MazeNode>
    </div>
    <template v-for="pos in ['top', 'right', 'bottom', 'left']" :key="pos">
      <MazeBorder
        :position="pos"
        :visible="state.borderVisible"
        class="maze__border"
      />
    </template>
  </div>
</template>

<style scoped lang="scss">
$cell-size: v-bind(cellSizeInPixels);

.maze {
  flex: 0 1 auto;
  position: relative;
  background-color: white;
}
.maze__row {
  display: flex;
}
.maze__row:first-child > .maze__node {
  margin-top: $cell-size;
}
.maze__node {
  margin: 0 $cell-size $cell-size 0;
  &:first-child {
    margin-left: $cell-size;
  }
}
</style>
