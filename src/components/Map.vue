<template>
  <div>
    <div
      style="max-width: 800px; margin: 0 auto; display: flex; align-items: center; justify-content: space-between"
    >
      <div>
        <h1>Minhas coordenadas:</h1>
        <p>
          {{ myCoordinates.lat }} Latitude, {{ myCoordinates.lng }} Longitude
        </p>
      </div>
      <div>
        <h1>Coordenadas mapa:</h1>
        <p>
          {{ mapCoordinates.lat }} Latitude, {{ mapCoordinates.lng }} Longitude
        </p>
      </div>
    </div>
    <GmapMap
      :center="myCoordinates"
      :zoom="zoom"
      style="width:640px; height:360px; margin: 32px auto;"
      ref="mapRef"
      @dragend="handleDrag"
      :options="{
        zoomControl: false,
        mapTypeControl: false,
        scaleControl: false,
        streetViewControl: false,
        rotateControl: false,
        fullscreenControl: false,
        disableDefaultUi: false,
      }"
    >
      <GmapCircle
        :center="mapCoordinates"
        :radius="150"
        :options="{ fillOpacity: 0.5, strokeWeight: 0.5, fillColor: 'blue' }"
      />
      <GmapMarker
        :position="mapCoordinates"
        :clickable="true"
        :draggable="true"
        @drag="updateMarker(index, $event.latLng)"
      />
    </GmapMap>
  </div>
</template>
<script>
import * as VueGoogleMaps from "vue2-google-maps";

export default {
  data() {
    return {
      map: null,
      myCoordinates: {
        lat: 0,
        lng: 0,
      },
      zoom: 7,
    };
  },
  created() {
    if (localStorage.center) {
      this.myCoordinates = JSON.parse(localStorage.center);
    } else {
      this.$getLocation({})
        .then((coordinates) => {
          this.myCoordinates = coordinates;
        })
        .catch((error) => alert(error));
    }

    if (localStorage.zoom) {
      this.zoom = parseInt(localStorage.zoom);
    }
  },
  mounted() {
    this.$refs.mapRef.$mapPromise.then((map) => (this.map = map));
  },
  methods: {
    updateMarker(index, location) {
      console.log(index, location);
    },
    handleDrag() {
      let center = {
        lat: this.map.getCenter().lat(),
        lng: this.map.getCenter().lng(),
      };
      let zoom = this.map.getZoom();
      localStorage.center = JSON.stringify(center);
      localStorage.zoom = zoom;
    },
  },
  computed: {
    google: VueGoogleMaps.gmapApi,
    mapCoordinates() {
      if (!this.map) {
        return {
          lat: 0,
          lng: 0,
        };
      }

      return {
        lat: Number(
          this.map
            .getCenter()
            .lat()
            .toFixed(4)
        ),
        lng: Number(
          this.map
            .getCenter()
            .lng()
            .toFixed(4)
        ),
      };
    },
  },
};
</script>
