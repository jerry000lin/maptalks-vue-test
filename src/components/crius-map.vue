<template>
  <div id="map">
    map
  </div>
</template>

<script>
import * as maptalks from "maptalks";
import shenzhen from "@/json/shenzhen.json";
export default {
  props: {
    initLon: {
      required: true,
      type: Number
    },
    initLat: {
      required: true,
      type: Number
    }
  },
  data() {
    return {
      map: null,
      areaLayer: null,
      drawTool: null,
      drawEndCallback: () => {}
    };
  },
  methods: {
    // 绘制圆形
    drawCircle(center, radius) {
      this.areaLayer.clear();
      const circle = new maptalks.Circle(center, radius, {
        symbol: {
          lineColor: "#1bbc9b",
          lineWidth: 2,
          polygonFill: "#fff",
          polygonOpacity: 0.3
        }
      });
      this.areaLayer.addGeometry(circle);
    },
    drawDistrict(districtName) {
      this.areaLayer.clear();
      const polygon = new maptalks.Polygon(shenzhen[districtName], {
        visible: true,
        editable: true,
        cursor: "pointer",
        shadowBlur: 0,
        shadowColor: "black",
        draggable: false,
        dragShadow: false, // display a shadow during dragging
        drawOnAxis: null, // force dragging stick on a axis, can be: x, y
        symbol: {
          lineColor: "#34495e",
          lineWidth: 2,
          polygonFill: "rgb(135,196,240)",
          polygonOpacity: 0.6
        }
      });
      this.areaLayer.addGeometry(polygon);
    },
    //绘制多边形，callback参数为多边形的点
    polygonDrawTool(callback) {
      this.drawEndCallback = callback;
      this.drawTool.enable();
    },
    // 取消绘制
    cancelDrawTool() {
      this.drawTool.disable();
    },
    // 清除绘制的内容
    clearOverlay() {
      this.areaLayer.clear();
    },
    //初始化drawTool
    initDrawTool() {
      this.drawTool = new maptalks.DrawTool({
        mode: "Polygon",
        symbol: {
          lineColor: "#1bbc9b",
          lineWidth: 2,
          polygonFill: "#fff",
          polygonOpacity: 0.3
        }
      })
        .addTo(this.map)
        .disable();
      this.drawTool.on("drawstart", () => {
        this.areaLayer.clear();
      });
      this.drawTool.on("drawend", param => {
        const coordinates = param.geometry.getCoordinates(); // x,y 需处理成可用对象
        // console.log(coordinates);
        this.areaLayer.addGeometry(param.geometry);
        this.drawEndCallback(coordinates);
      });
    }
  },
  mounted() {
    this.map = new maptalks.Map("map", {
      center: [this.initLon, this.initLat],
      zoom: 14,
      baseLayer: new maptalks.TileLayer("base", {
        urlTemplate:
          "http://61.144.226.43:8081/yxdt2kdata1/rest/services/MapServer/SZIMAGE_CIMAP_WGS84_201612/MapServer/tile/{z}/{y}/{x}"
      })
    });

    this.areaLayer = new maptalks.VectorLayer("drawtool").addTo(this.map);

    this.initDrawTool();
  }
};
</script>

<style lang="less" scoped>
#map {
  width: 100%;
  height: 100%;
}
</style>
