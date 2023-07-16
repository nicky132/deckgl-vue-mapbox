<template>
  <VueDeckgl
    :layers="layers"
    class="vc"
    :viewState="viewState"
    @view-state-change="updateViewState"
  >
    <div id="map" ref="map"></div>
  </VueDeckgl>
</template>

<script>
import { Tile3DLayer } from "@deck.gl/geo-layers";
import { CesiumIonLoader } from "@loaders.gl/3d-tiles";

import mapboxgl from "mapbox-gl";
import VueDeckgl from "vue-deck.gl";

export default {
  components: {
    VueDeckgl,
  },
  data() {
    return {
      viewState: {
        latitude: 30.5,
        longitude: 121,
        zoom: 4,
        bearing: 0,
        pitch: 0,
      },
      mapStyle: "mapbox://styles/haxzie/ck0aryyna2lwq1crp7fwpm5vz",
      accessToken:
        "pk.eyJ1Ijoiam9yb21lcm8iLCJhIjoiY2toa2t2NnBjMDJkYTJzcXQyZThhZTNyNSJ9.Wx6qT7xWJ-hhKHyLMNbnAQ",
    };
  },
  computed: {
    layers() {
      const layer = new Tile3DLayer({
        id: "tile-3d-layer",
        data: "tileset.json", //replace with your tileset
        loader: CesiumIonLoader,
        loadOptions: {
          // 'cesium-ion': { accessToken: 'eyJhbGciOiJIUzI1'  }//replace with your token
        },
        onTileError: (err) => {
          console.log(err);
        },
        onTilesetLoad: (tileset) => {
          tileset;
          const { cartographicCenter, zoom } = tileset;
          this.viewState = {
            ...this.viewState,
            longitude: cartographicCenter[0],
            latitude: cartographicCenter[1],
            zoom,
          };
        },
        _subLayerProps: {
          scenegraph: { _lighting: "flat" },
        },
      });
      return [layer];
    },
  },
  created() {
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
    hnadleClick() {
      console.log("a");
    },
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
#map,
.vc {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #e5e9ec;
  overflow: hidden;
}
</style>
