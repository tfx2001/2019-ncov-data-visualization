<template>
  <div id="canvas"></div>
</template>

<script>
import { Line } from "@antv/g2plot";
var linePlot;
export default {
  name: "data-chart",
  props: {
    plotData: Array
  },
  data: () => ({}),
  mounted() {
    linePlot = new Line("canvas", {
      data: this.plotData,
      xField: "year",
      yField: "value"
    });

    linePlot.render();
  },
  watch: {
    plotData: function(val) {
      // eslint-disable-next-line no-console
      console.log(val);
      linePlot.changeData(this.plotData);
    }
  }
};
</script>

<style>
</style>