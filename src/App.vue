<!-- eslint-disable no-unused-vars -->
<script>
import CardContainer from "./components/CardContainer.vue";
import { WEATHER_DATA } from "./mockData";

const API_KEY = "09a763398032247c5ca506d40b4fd6ea";

export default {
  data() {
    return {
      json: WEATHER_DATA
  }
 },
 methods: {
    fetchData () {
      
    },
    fetchCity () {

    },
 },
 components: {
  CardContainer
 },
}
</script>

<template>
  <div class="container">
    <CardContainer :data="json"></CardContainer>
  </div>
</template>

<style scoped></style>
