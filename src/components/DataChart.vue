<template>
  <div>
    <h4 v-show="title.length" class="mx-6">{{ title }}每日疫情趋势</h4>
    <div id="canvas"></div>
  </div>
</template>

<script>
import { Line } from "@antv/g2plot";
var linePlot;
export default {
  name: "data-chart",
  props: {
    chartData: Array,
    title: String
  },
  data: () => ({}),
  mounted() {
    linePlot = new Line("canvas", {
      data: this.chartData,
      xField: "date",
      yField: "value",
      seriesField: "type",
      forceFit: true,
      padding: "auto",
      point: {
        visible: true,
        size: 5
      },
      xAxis: {
        autoHideLabel: true
      },
      interactions: [
        {
          type: "slider"
        }
      ],
      lineSize: 3,
      smooth: true,
      color(val) {
        switch (val) {
          case "确诊":
            return "rgb(247, 76, 49)";
          case "治愈":
            return "rgb(40, 183, 163)";
          case "重症":
            return "rgb(162, 90, 78)";
          case "死亡":
            return "rgb(93, 112, 146)";
        }
      }
    });

    linePlot.render();
  },
  watch: {
    // eslint-disable-next-line no-unused-vars
    chartData(val) {
      linePlot.changeData(this.chartData);
    }
  }
};
</script>

<style scoped>
h4 {
  user-select: none;
}
</style>