<template>
  <VueDeckgl
    :layers="layers"
    :viewState="viewState"
    @click="handleClick"
    @view-state-change="updateViewState"
  >
    <div id="map" ref="map"></div>
  </VueDeckgl>
</template>

<script>
import { PathLayer } from "@deck.gl/layers";
const data = require("../assets/routes-data.json");
import mapboxgl from "mapbox-gl";
import VueDeckgl from "vue-deck.gl";

export default {
  components: {
    VueDeckgl,
  },
  data() {
    return {
      // accessToken: process.env.VUE_APP_MAPBOX_TOKEN,
      accessToken:
        "pk.eyJ1Ijoiam9yb21lcm8iLCJhIjoiY2toa2t2NnBjMDJkYTJzcXQyZThhZTNyNSJ9.Wx6qT7xWJ-hhKHyLMNbnAQ",
      mapStyle: "mapbox://styles/haxzie/ck0aryyna2lwq1crp7fwpm5vz",
      pathData: data,
      // pathData:
      //   "https://raw.githubusercontent.com/visgl/deck.gl-data/master/website/bart-lines.json",
      viewState: {
        latitude: 12.976387,
        longitude: 77.571529,
        zoom: 4,
        bearing: 0,
        pitch: 0,
      },
    };
  },
  computed: {
    layers() {
      const paths = new PathLayer({
        id: "path-layer",
        data: this.pathData,
        // data: "https://raw.githubusercontent.com/visgl/deck.gl-data/master/website/bart-lines.json",
        widthScale: 20,
        widthMinPixels: 2,
        getPath: (d) => d.path,
        getColor: (d) => [255, (1 - d.data.distance / 100) * 255, 0],
        getWidth: (d) => 2,
      });
      return [paths];
    },
  },
  created() {
    // We need to set mapbox-gl library here in order to use it in template
    this.map = null;
  },
  methods: {
    updateViewState(viewState) {
      console.log("updating view state...");
      this.viewState = {
        ...viewState,
      };
      this.map.jumpTo({
        center: [viewState.longitude, viewState.latitude],
        zoom: viewState.zoom,
        bearing: viewState.bearing,
        pitch: viewState.pitch,
      });
    },
    hnadleClick({ event, info }) {},
  },
  mounted() {
    // creating the map
    this.map = new mapboxgl.Map({
      accessToken: this.accessToken,
      container: this.$refs.map,
      interactive: false,
      style:
        this.mapStyle || "mapbox://styles/haxzie/ck7h838qb0bik1iofe0k2i3f2",
      center: [this.viewState.longitude, this.viewState.latitude],
      zoom: this.viewState.zoom,
      pitch: this.viewState.pitch,
      bearing: this.viewState.bearing,
    });

    setTimeout(() => {
      const { latitude, longitude, pitch, bearing, zoom } = this.viewState;
      this.viewState = {
        latitude,
        longitude,
        pitch,
        bearing,
        zoom: 12,
        transitionDuration: 3000,
      };
    }, 5000);
  },
};
</script>

<style lang="css">
#map {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #e5e9ec;
  overflow: hidden;
}
</style>
