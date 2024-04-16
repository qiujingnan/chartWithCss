<script setup>
import { ref } from "vue";
import pieChart from "../packages/pieChart.vue";
const chartData = ref([
  { myLabel: "Draft", myValue: 60, myColor: "#EFF1F2" },
  { myLabel: "In Progress", myValue: 10, myColor: "#007BC0" },
  { myLabel: "Approved", myValue: 25, myColor: "#419E98" },
  { myLabel: "Rejected", myValue: 40, myColor: "#F19F23" },
]);
const chartData1 = ref([
  { label: "Draft", value: 30, color: "#E3F1F2" },
  { label: "In Progress", value: 10, color: "#027BC0" },
  { label: "Approved", value: 55, color: "#419E38" },
  { label: "Rejected", value: 40, color: "#F13F23" },
]);
const handleClick = (params) => {
  alert(`click ${params.myLabel}`);
};
</script>

<template>
  <div class="testChart">
    <pieChart :chartData="chartData1"> </pieChart>
    <br />
    <br />
    <br />
    <br />
    <pieChart
      :chartData="chartData"
      :fieldNames="{ label: 'myLabel', value: 'myValue', color: 'myColor' }"
      ringWitdh="30%"
      :handleClick="handleClick"
    >
      <template #centerText>
        <p>自定义中心文字</p>
      </template>
    </pieChart>
  </div>
</template>

<style scoped>
.testChart {
  margin: 0 auto;
  width: 200px;
  height: 200px;
}
</style>
