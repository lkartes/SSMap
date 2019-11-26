<template>
  <ssmap></ssmap>
</template>

<script>
import ssmap from "./components/Map.vue";
export default {
  name: "App",
  components: {
    ssmap
  },
  computed: {},
  methods: {}
};
</script>

<style lang="scss">
@import "../node_modules/bootstrap/scss/bootstrap.scss";
@import "../node_modules/leaflet/dist/leaflet.css";
</style>
