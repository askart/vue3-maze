<script>
import GridJoint from "./GridJoint.vue";
import GridJointLine from "./GridJointLine.vue";
import GridBorder from "./GridBorder.vue";

import { provide } from "@vue/runtime-core";

export default {
  components: {
    GridJoint,
    GridJointLine,
    GridBorder,
  },
  props: {
    height: {
      type: Number,
      default: null,
    },
    width: {
      type: Number,
      default: null,
    },
    jointMatrix: {
      type: Array,
      default: () => [],
    },
    hidden: {
      type: Boolean,
      default: false,
    },
    dotSizeInPixels: {
      type: String,
      default: "",
    },
    cellSizeInPixels: {
      type: String,
      default: "",
    },
  },
  setup(props) {
    provide("verticalCellsCount", props.height);
    provide("horizontalCellsCount", props.width);
    provide("dotSize", props.dotSizeInPixels);
    provide("cellSize", props.cellSizeInPixels);
    provide("mazeColor", "gray");
  },
};
</script>

<template>
  <div class="grid">
    <div
      v-for="(row, rowIndex) in jointMatrix"
      :key="rowIndex"
      class="grid__row"
    >
      <GridJoint
        v-for="(joint, jointIndex) in row"
        :key="jointIndex"
        class="grid__joint"
      >
        <GridJointLine position="top" :hidden="!joint.TOP" />
        <GridJointLine position="right" :hidden="!joint.RIGHT" />
        <GridJointLine position="bottom" :hidden="!joint.BOTTOM" />
        <GridJointLine position="left" :hidden="!joint.LEFT" />
      </GridJoint>
    </div>
    <GridBorder position="top" :hidden="hidden" class="grid__border" />
    <GridBorder position="right" :hidden="hidden" class="grid__border" />
    <GridBorder position="bottom" :hidden="hidden" class="grid__border" />
    <GridBorder position="left" :hidden="hidden" class="grid__border" />
  </div>
</template>

<style scoped lang="scss">
$cell-size: v-bind(cellSizeInPixels);

.grid {
  flex: 0 1 auto;
  position: relative;
  background-color: white;
}
.grid__row {
  display: flex;
}
.grid__row:first-child > .grid__joint {
  margin-top: $cell-size;
}
.grid__joint {
  margin: 0 $cell-size $cell-size 0;
  &:first-child {
    margin-left: $cell-size;
  }
}
</style>
