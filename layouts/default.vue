<template>
  <div>
    <navbar></navbar>
    <nuxt />
  </div>
</template>
<script lang="ts">
import Vue from "vue";
import navbar from "../components/navbar.vue";
export default {
  components: {
    navbar,
  },
  name: "default",
};
</script>
