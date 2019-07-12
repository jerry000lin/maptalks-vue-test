<template>
  <div id="map">
    map
  </div>
</template>

<script>
import * as maptalks from "maptalks";
import "maptalks/dist/maptalks.css";
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
      markLayer: null, // 项目过程中不会删除的点，初始化时加入
      pointLayer: null, // 项目过程中会切换的点，可选择加入。。
      areaLayer: null,
      roadLayer: null,
      drawTool: null,
      drawEndCallback: () => {}
    };
  },
  methods: {
    addMarker() {
      const marker = new maptalks.Marker([this.initLon, this.initLat], {
        cursor: "pointer",
        symbol: {
          markerFile:
            window.location.origin + require("@/assets/mark.png").toString(),
          markerWidth: 28,
          markerHeight: 40
        }
      }).addTo(this.markLayer);

      marker.setInfoWindow({
        title: "Marker's InfoWindow",
        content: "Click on marker to open.",
        autoPan: true,
        width: 300,
        minHeight: 120,
        custom: false,
        autoOpenOn: "click", //set to null if not to open when clicking on marker
        autoCloseOn: "click"
      });

      marker.openInfoWindow();
    },
    addPointGroup(pointArray) {
      this.pointLayer.clear();
      new maptalks.MultiPoint(pointArray, {
        cursor: "pointer",
        symbol: {
          markerFile:
            window.location.origin + require("@/assets/point.png").toString(),
          markerWidth: 28,
          markerHeight: 40
        }
      }).addTo(this.pointLayer);
    },
    clearPointGroup() {
      this.pointLayer.clear();
    },
    drawLine(pointArray) {
      if (this.roadLayer == null) {
        this.roadLayer = new maptalks.VectorLayer("roadLayer").addTo(this.map);
      }
      this.roadLayer.clear();
      new maptalks.LineString(pointArray, {
        cursor: "default",
        symbol: {
          lineColor: "#409EFF",
          lineWidth: 3
        }
      }).addTo(this.roadLayer);
    },

    drawCircle(center, radius) {
      this.areaLayer.clear();
      const circle = new maptalks.Circle(center, radius, {
        cursor: "default",
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
        cursor: "default",
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
    // 清除区域的内容
    clearAreaOverlay() {
      this.areaLayer.clear();
    },

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
        const coordinates = param.geometry.getCoordinates();
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

    this.areaLayer = new maptalks.VectorLayer("areaLayer").addTo(this.map);
    this.initDrawTool(); //drawTool只创建一次，避免回调函数被多次建立

    this.markLayer = new maptalks.VectorLayer("markLayer").addTo(this.map);
    this.addMarker();

    this.pointLayer = new maptalks.VectorLayer("pointLayer").addTo(this.map);
  }
};
</script>

<style lang="less" scoped>
#map {
  width: 100%;
  height: 100%;
}
</style>
