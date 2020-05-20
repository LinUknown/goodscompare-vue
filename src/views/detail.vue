<template>
  <div>
    <a-breadcrumb style="margin: 16px 0">
      <a-breadcrumb-item @click="clickone">首页</a-breadcrumb-item>
      <a-breadcrumb-item>商品</a-breadcrumb-item>
      <a-breadcrumb-item>价格波动</a-breadcrumb-item>
    </a-breadcrumb>
    <div id="myChart" :style="{ width: '1000px', height: '500px' }"></div>

    <div :style="{ background: '#fff', padding: '24px', minHeight: '280px' }">
      Content
    </div>
  </div>
</template>

<script>
import axios from "axios";
import echarts from "echarts";

export default {
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let eid = this.$route.params.eid;
      let goodID = this.$route.params.goodID;
      console.log(eid);
      console.log(goodID);
      axios
        .get("/goods/price_history", {
          params: {
            eid: eid,
            gid: goodID
          }
        })
        .then(res => {
          // console.log();
          let goods = res.data.result;
          let data = [];
          let dataTime = [];
          for (let i = 0; i < goods.length; i++) {
            data.push(goods[i].Price);
            if (i == 0) {
              dataTime.push("今天");
            } else {
              dataTime.push(i + "天前");
            }
          }
          console.log(data);
          this.drawLine("myChart", data, dataTime);
        });
    },
    drawLine(id, arr, arrTime) {
      this.charts = echarts.init(document.getElementById(id));
      this.charts.setOption({
        tooltip: {
          trigger: "axis"
        },
        legend: {
          data: ["价格"]
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },

        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        xAxis: {
          type: "category",
          boundaryGap: false,
          data: arrTime
        },
        yAxis: {
          type: "value"
        },

        series: [
          {
            name: "arrTime",
            type: "line",
            stack: "总量",
            data: arr
          }
        ]
      });
    }
  }
};
</script>

<style></style>
