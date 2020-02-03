<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center title">2019-nCov新型冠状病毒疫情每日数据可视化</div>
    </v-app-bar>
    <v-content>
      <v-container fluid>
        <v-row justify="center">
          <v-col cols="12" xs="12" sm="8" lg="4">
            <v-row justify="center" class="mx-2">
              <v-col>
                <v-select v-model="currentProvince" :items="provinces" label="省/直辖市" outlined></v-select>
              </v-col>
              <v-col>
                <v-select :items="cities" label="市" outlined no-data-text="请先选择省/直辖市"></v-select>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
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
    currentProvince: ""
  }),
  computed: {
    cities() {
      let res = [];
      if (this.citiesRaw[this.currentProvince]) {
        for (const iterator of this.citiesRaw[this.currentProvince]) {
          res.push(iterator.name);
        }
      }
      return res;
    }
  },
  methods: {},
  created() {
    axios.get("/json/province.json").then(response => {
      for (const index of response.data) {
        this.provinces.push({ text: index.name, value: index.id });
      }
    });
    axios
      .get("/json/city.json")
      .then(response => (this.citiesRaw = response.data));
  }
};
</script>
