<template>
  <div id="canvas"></div>
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
      title: {
        visible: false,
        text: `${this.title}每日疫情趋势`
      },
      label: {
        visible: true
      },
      point: {
        visible: true,
        size: 5
      },
      xAxis: {
        autoHideLabel: true
      },
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
    chartData: function(val) {
      // eslint-disable-next-line no-console
      linePlot.changeData(this.chartData);
      linePlot.updateConfig({
        title: {
          visible: true,
          text: `${this.title}每日疫情趋势`
        }
      });
      linePlot.render();
    }
  }
};
</script>

<style>
</style>