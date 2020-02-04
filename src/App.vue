<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center title">2019-nCov每日疫情数据可视化</div>
    </v-app-bar>
    <v-content>
      <v-container fluid>
        <v-row justify="center">
          <v-col cols="12" xs="12" sm="8" lg="4">
            <v-row justify="center" class="mx-2">
              <v-col>
                <v-select
                  v-model="currentProvince"
                  :items="provinces"
                  label="省/直辖市"
                  @change="onProvinceSelectChange"
                  outlined
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="citiesName"
                  label="市"
                  outlined
                  no-data-text="请先选择省/直辖市"
                  @change="onCitySelectChange"
                  clearable
                ></v-select>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col>
                <data-chart :chart-data="chartData" :title="chartTitle" class="mt-n10"></data-chart>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mx-2">
                <h1>说明</h1>
                <p>
                  疫情数据来源
                  <a
                    href="https://github.com/norratek/Ncov2020HistoryData"
                  >norratek/Ncov2020HistoryData</a>。
                  <br />由于该数据库的数据由人工统计（各省卫健委每日疫情情况），所以具体数据请以各省卫健发布为准。
                </p>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
    <v-footer app>
      <v-row>
        <v-col class="text-center">
          Proudly&nbsp;developed&nbsp;with<a href="https://vuetifyjs.com/" class="mx-1 font-weight-medium">Vuetify</a>&<a href="https://g2plot.antv.vision/" class="mx-1 font-weight-medium">G2Plot</a>
        </v-col>
      </v-row>
    </v-footer>
  </v-app>
</template>

<script>
import { DataChart } from "./components";
import axios from "axios";

export default {
  name: "App",

  // eslint-disable-next-line vue/no-unused-components
  components: { DataChart },

  data: () => ({
    provinces: [],
    citiesRaw: [],
    currentProvince: {},
    chartData: [],
    chartTitle: ""
  }),
  computed: {
    citiesName() {
      let res = [];
      if (this.citiesRaw[this.currentProvince.id]) {
        for (const iterator of this.citiesRaw[this.currentProvince.id]) {
          res.push(iterator.name);
        }
      }
      return res;
    }
  },
  methods: {
    onCitySelectChange(val) {
      if (val && val != "市辖区") {
        axios
          .get(`http://ncov.nosensor.com:8080/api/city=${val}`)
          .then(response => {
            let res = [];
            for (const iterator of response.data[
              val == "市辖区" ? "province" : "city"
            ]) {
              let date = (() => {
                let ymd = iterator.Date.split("-");
                return `${ymd[1]}-${ymd[2]}`;
              })();
              res.push({ date, type: "确诊", value: iterator.Confirmed });
              res.push({ date, type: "治愈", value: iterator.Cured });
              res.push({ date, type: "死亡", value: iterator.Dead });
              res.push({ date, type: "重症", value: iterator.Severe });
            }
            res = res.reverse();
            this.chartData = res;
            this.chartTitle = val;
          });
      } else {
        this.onProvinceSelectChange(this.currentProvince);
      }
    },
    onProvinceSelectChange(val) {
      axios
        .get(
          `http://ncov.nosensor.com:8080/api/province=${val.name.slice(0, 2)}`
        )
        .then(response => {
          let res = [];
          for (const iterator of response.data["province"]) {
            let date = (() => {
              let ymd = iterator.Date.split("-");
              return `${ymd[1]}-${ymd[2]}`;
            })();
            res.push({ date, type: "确诊", value: iterator.Confirmed });
            res.push({ date, type: "治愈", value: iterator.Cured });
            res.push({ date, type: "死亡", value: iterator.Dead });
            res.push({ date, type: "重症", value: iterator.Severe });
          }
          res = res.reverse();
          this.chartData = res;
          this.chartTitle = val.name;
        });
    }
  },
  created() {
    axios.get("json/province.json").then(response => {
      for (const index of response.data) {
        this.provinces.push({
          text: index.name,
          value: { name: index.name, id: index.id }
        });
      }
    });
    axios
      .get("json/city.json")
      .then(response => (this.citiesRaw = response.data));
  }
};
</script>
