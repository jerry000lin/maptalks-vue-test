<template>
  <div class="main-warpper">
    <crius-map :initLon="lon" :initLat="lat" ref="criusMap"></crius-map>
    <div class="barrrr">
      <button @click="polygonDrawTool()">drawPolygon</button>
      <button @click="clear()">clear</button>
      <button @click="cancelDrawTool()">disable</button>

      <br />

      <button @click="drawCircle(1)">圆1</button>
      <button @click="drawCircle(2)">圆2</button>
      <button @click="drawCircle(3)">圆3</button>

      <br />
      <button
        v-for="district in shenzhen"
        :key="district"
        @click="changeDistrict(district)"
      >
        {{ district }}
      </button>
      <br />
      <button @click="drawLine">线</button>
      <br />
      <button
        @click="
          addPointGroup([
            [114.11040469115796, 22.639832751648206],
            [114.13240469115796, 22.661832751648206]
          ])
        "
      >
        点1
      </button>

      <button
        @click="
          addPointGroup([
            [114.10940469115796, 22.638832751648206],
            [114.13340469115796, 22.665832751648206]
          ])
        "
      >
        点2
      </button>
    </div>
  </div>
</template>

<script>
import criusMap from "@/components/crius-map";

export default {
  data() {
    return {
      lon: 114.12140469115796,
      lat: 22.650832751648206,
      shenzhen: [
        "罗湖区",
        "南山区",
        "福田区",
        "盐田区",
        "大鹏新区",
        "宝安区",
        "光明新区",
        "龙华新区",
        "坪山新区",
        "龙岗区"
      ]
    };
  },
  methods: {
    polygonDrawTool() {
      this.$refs.criusMap.polygonDrawTool(coordinates => {
        console.log(coordinates);
      });
    },
    clear() {
      this.$refs.criusMap.clearAreaOverlay();
    },
    cancelDrawTool() {
      this.$refs.criusMap.cancelDrawTool();
    },
    drawCircle(n) {
      this.$refs.criusMap.drawCircle([this.lon, this.lat], 1000 * n);
    },
    changeDistrict(district) {
      this.$refs.criusMap.drawDistrict(district);
    },
    drawLine() {
      this.$refs.criusMap.drawLine([
        [114.11040469115796, 22.639832751648206],
        [114.13240469115796, 22.661832751648206]
      ]);
    },
    addPointGroup(pointArray) {
      this.$refs.criusMap.addPointGroup(pointArray);
    }
  },
  components: {
    criusMap
  }
};
</script>

<style lang="less" scoped>
.main-warpper {
  user-select: none;
  position: relative;
  height: 100%;
  width: 100%;
  .barrrr {
    position: absolute;
    top: 20px;
    right: 20px;
  }
}
</style>
