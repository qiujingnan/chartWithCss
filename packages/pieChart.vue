<script setup>
import { computed, onMounted, reactive, ref, watch } from "vue";
const props = defineProps({
  chartData: {
    type: Array,
    require: true,
    default: undefined,
  },
  fieldNames: {
    type: Object,
    require: false,
    default: undefined,
  },
  ringWitdh: {
    type: String,
    require: false,
    default: "20%",
  },
  handleClick: {
    type: Function,
    require: false,
    default: () => {},
  },
});
const pieChartContainer = ref();
const hasReady = ref(false);
const pieStyle = reactive({}); //外层大圆的样式
const totalNum = ref(0);
watch(
  props.chartData,
  () => {
    calculatePieStyles();
  },
  {
    deep: true,
  }
);
const execPoint = (cx, cy, r, ratio) => {
  const rad = ratio * 2 * Math.PI;
  return {
    x: cx + Math.sin(rad) * r,
    y: cy - Math.cos(rad) * r,
  };
};
const calculatePieStyles = () => {
  const selfLabel = props?.fieldNames?.label || "label";
  const selfValue = props?.fieldNames?.value || "value";
  const selfColor = props?.fieldNames?.color || "color";
  totalNum.value = props.chartData.reduce(
    (total, data) => total + data[selfValue],
    0
  );
  const containerWidth = pieChartContainer.value.offsetWidth;
  const containerHeight = pieChartContainer.value.offsetHeight;
  // 圆心坐标
  const cx = containerWidth / 2;
  const cy = containerHeight / 2;
  // 半径
  const r = Math.min(containerWidth, containerHeight) / 2;
  // 偏转量
  let offset = 0;
  props.chartData.forEach((item) => {
    const ratio = item[selfValue] / totalNum.value;
    let path = `M ${cx},${cy}`;
    // 圆弧起点
    const start = execPoint(cx, cy, r, offset);
    path += ` L ${start.x},${start.y}`;
    // 圆弧终点
    offset += ratio;
    const end = execPoint(cx, cy, r, offset);
    const angle = ratio * 2 * Math.PI;
    path += ` A ${r},${r} 0,${angle > Math.PI ? 1 : 0},1 ${end.x},${end.y}`;
    path += " Z";
    pieStyle[item[selfLabel]] = {
      clipPath: `path('${path}')`,
      backgroundColor: item[selfColor],
    };
    hasReady.value = true;
  });
};

const innerPieStyle = computed(() => {
  const innerPieStyle = {};
  innerPieStyle.width = `calc(100% - ${props.ringWitdh})`;
  innerPieStyle.height = `calc(100% - ${props.ringWitdh})`;
  return innerPieStyle;
});

onMounted(() => {
  calculatePieStyles();
});
</script>

<template>
  <div class="pie-chart" ref="pieChartContainer">
    <div
      v-for="(data, index) of props.chartData"
      class="slice"
      :id="`slice${index}`"
      :key="index"
      :style="pieStyle[data[props?.fieldNames?.label || 'label']]"
      @click="props.handleClick(data)"
    ></div>

    <template v-if="hasReady">
      <div class="center" :style="props.ringWitdh ? innerPieStyle : null">
        <slot name="centerText">
          <div class="center-text">
            <div>Total</div>
            <div>{{ totalNum }}</div>
          </div>
        </slot>
      </div>
    </template>
  </div>
</template>

<style scoped>
.pie-chart {
  position: relative;
  /* 给悬浮变大预留一些空间 */
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background-color: fff;
}

.slice {
  position: absolute;
  width: 100%;
  height: 100%;
  cursor: pointer;
  transition: all 0.2s ease-in;
}
.tooltip {
  /* visibility: hidden; */
  width: 120px;
  background-color: black;
  color: #fff;
  text-align: center;
  padding: 5px 0;
  border-radius: 6px;
  position: absolute;
  left: 20px;
  z-index: 100;
}
.slice:hover {
  transform: scale(1.05);
}

.center {
  position: absolute;
  width: 80%;
  height: 80%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  background-color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
}
.center-text {
  align-items: center;
  text-align: center;
}
.center-text div:nth-child(1) {
  font-size: 18px;
  font-weight: 500;
  line-height: 22.85px;
}
.center-text div:nth-child(2) {
  font-size: 32px;
  font-weight: 700;
  line-height: 38.73px;
}
</style>
