<template>
  <div class="container">
    <div id="map" ref="map"></div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
export default {
  data() {
    return {
      accessToken:
        "pk.eyJ1Ijoiam9yb21lcm8iLCJhIjoiY2toa2t2NnBjMDJkYTJzcXQyZThhZTNyNSJ9.Wx6qT7xWJ-hhKHyLMNbnAQ",
      mapStyle: "mapbox://styles/haxzie/ck0aryyna2lwq1crp7fwpm5vz",
      mapData:
        "https://raw.githubusercontent.com/johan/world.geo.json/master/countries.geo.json",
      latitude: 35.4832668,
      longitude: 12.9491635,
      zoom: 10,
      bearing: 0,
      pitch: 0,
      viewState: {
        latitude: 40.6971494,
        longitude: -74.2598655,
        zoom: 12,
        pitch: 0,
      },
    };
  },
  created() {
    // creating a non reactive map object
    this.map = null;
  },
  mounted() {
    // initialize the map
    this.map = new mapboxgl.Map({
      accessToken: this.accessToken,
      container: this.$refs.map,
      style: this.mapStyle,
      center: [this.viewState.longitude, this.viewState.latitude],
      zoom: this.viewState.zoom,
      pitch: this.viewState.pitch,
      bearing: this.viewState.bearing,
    });
  },
};
</script>

<style lang="css">
.map-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}
</style>
