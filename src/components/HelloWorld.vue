<template>
  <div class="container">
    <div id="map" ref="map"></div>
  </div>
</template>

<script>
import { Deck } from "@deck.gl/core";
import mapboxgl from "mapbox-gl";
import { PathLayer } from "@deck.gl/layers";
// const data = require("../assets/routes-data.json");
// const data = require("../assets/Production_1.json");
// http://cloudv2bucket.oss-cn-shanghai.aliyuncs.com/185/1254/resultCC/Production_1.json

// const data = require("../assets/countries.json");
// https://raw.githubusercontent.com/johan/world.geo.json/master/countries.geo.json

const data = require("../assets/china.json");

export default {
  data() {
    return {
      viewState: {
        latitude: 35.4832668,
        longitude: 12.9491635,
        zoom: 12,
        pitch: 0,
        bearing: 0,
      },
      pathData: data,
    };
  },
  computed: {
    // a reactive property which creates the layer objects whenever the data is changed
    getLayers() {
      const paths = new PathLayer({
        id: "path-layer",
        data: this.pathData,
        widthScale: 20,
        widthMinPixels: 2,
        getPath: (d) => d.path,
        getColor: (d) => d.color,
        getWidth: () => 1,
      });

      return [paths];
    },
  },
  created() {
    this.map = null;
    this.deck = null;
  },
  methods: {
    renderLayers(layers) {
      // setting the layers to deck.gl props
      this.deck.setProps({
        layers,
      });
    },
  },
  watch: {
    // whenever the layer data is changed and new layers are created,
    // rerender the layers
    getLayers(layers) {
      this.renderLayers(layers);
    },
  },
  mounted() {
    // creating the map
    this.map = new mapboxgl.Map({
      accessToken:
        "pk.eyJ1Ijoiam9yb21lcm8iLCJhIjoiY2toa2t2NnBjMDJkYTJzcXQyZThhZTNyNSJ9.Wx6qT7xWJ-hhKHyLMNbnAQ",
      container: this.$refs.map,
      interactive: false,
      style:
        this.mapStyle || "mapbox://styles/haxzie/ck0aryyna2lwq1crp7fwpm5vz",
      center: [this.viewState.longitude, this.viewState.latitude],
      zoom: this.viewState.zoom,
      pitch: this.viewState.pitch,
      bearing: this.viewState.bearing,
    });

    // creating the deck.gl instance
    this.deck = new Deck({
      canvas: this.$refs.canvas,
      width: "100%",
      height: "100%",
      initialViewState: this.viewState,
      controller: true,
      // change the map's viewstate whenever the view state of deck.gl changes
      onViewStateChange: ({ viewState }) => {
        this.map.jumpTo({
          center: [viewState.longitude, viewState.latitude],
          zoom: viewState.zoom,
          bearing: viewState.bearing,
          pitch: viewState.pitch,
        });
      },
    });
  },
};
</script>

<style lang="css">
/* #map {
  width: 100%;
  height: 100vh;
  position: relative;
  overflow: hidden;
} */
.deck-container {
  width: 100%;
  height: 100%;
  position: relative;
}
#map {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #e5e9ec;
  overflow: hidden;
}
#deck-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
