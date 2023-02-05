<!-- eslint-disable no-unused-vars -->
<script>
import CardContainer from "./components/CardContainer.vue";
import { WEATHER_DATA } from "./mockData";

const API_KEY = "09a763398032247c5ca506d40b4fd6ea";

export default {
  data() {
    return {
      json: WEATHER_DATA,
      title: "",
  }
 },
 methods: {
    fetchData (lat,lon) {
      fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${API_KEY}`)
      .then(res=>res.json())
      .then(json=>{
        this.json=json;
      })

    },
    fetchCity () {
      fetch(`http://api.openweathermap.org/geo/1.0/direct?q=Saint-Petersburg&limit=1&appid=${API_KEY}`)
      .then(data=>data.json())
      .then(json=>{
        this.title = `${json[0].name}, ${json[0].country}`
        this.fetchData(json[0].lat.toFixed(2),json[0].lon.toFixed(2));
      })
    },
 },
 mounted (){
  this.fetchCity();
 },
 components: {
  CardContainer
 },
}
</script>

<template>
  <div class="container">
    <CardContainer :data="json" :title="title"></CardContainer>
  </div>
</template>

<style scoped></style>
