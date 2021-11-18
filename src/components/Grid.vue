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
    verticalCellsCount: {
      type: Number,
      default: 10,
    },
    horizontalCellsCount: {
      type: Number,
      default: 15,
    },
    dotSize: {
      type: String,
      default: "2px",
    },
    jointMatrix: {
      type: Array,
      default: () => [],
    },
    hidden: {
      type: Boolean,
      default: false,
    },
  },
  setup(props) {
    const length = `calc(50px - ${props.dotSize})`;

    provide("verticalCellsCount", props.verticalCellsCount);
    provide("horizontalCellsCount", props.horizontalCellsCount);
    provide("dotSize", props.dotSize);
    provide("length", length);
    provide("mazeColor", "gray");

    return {
      length,
    };
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
$length: v-bind(length);

.grid {
  flex: 0 1 auto;
  position: relative;
  background-color: white;
}
.grid__row {
  display: flex;
}
.grid__row:first-child > .grid__joint {
  margin-top: $length;
}
.grid__joint {
  margin: 0 $length $length 0;
  &:first-child {
    margin-left: $length;
  }
}
</style>
