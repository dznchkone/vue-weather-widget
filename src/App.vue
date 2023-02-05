<!-- eslint-disable no-unused-vars -->
<script>
import CardComponent from "./components/WeatherCard.vue";
import SettingsPage from "./pages/SettingsPage.vue";
import API_KEY from "./constants";

export default {
  data() {
    return {
      json: null,
      title: "",
      settings: []
    };
  },
  methods: {
    fetchReverseCity(lat, lon) {
      fetch(
        `http://api.openweathermap.org/geo/1.0/reverse?lat=${lat}&lon=${lon}&limit=5&appid=${API_KEY}`
      )
        .then((data) => data.json())
        .then((json) => {
          this.title = `${json[0].name}, ${json[0].country}`;
          this.fetchData(json[0].lat.toFixed(2), json[0].lon.toFixed(2));
        });
    },
    fetchData(lat, lon) {
      fetch(
        `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${API_KEY}`
      )
        .then((res) => res.json())
        .then((json) => {
          this.json = json;
        });
    },
    onSettingsChange (newSettings) {
      this.settings = [...newSettings];
      window.localStorage.setItem("settings",JSON.stringify(newSettings));
    }
  },
  created() {
    const successCallback = (position) => {
      const currentPosition = {
        lat: position.coords.latitude.toFixed(2),
        lon: position.coords.longitude.toFixed(2),
      };
      window.localStorage.setItem(
        "settings",
        JSON.stringify([{id: 0, location: currentPosition }])
      );
      this.fetchReverseCity(currentPosition.lat, currentPosition.lon);
    };

    const errorCallback = (error) => {
      console.log(error);
    };

    const localStorage = window.localStorage.getItem("settings");

    if (!localStorage) {
      navigator.geolocation.getCurrentPosition(successCallback, errorCallback);
    } else {
      this.settings = JSON.parse(localStorage);
      const { lat, lon } = this.settings[0].location;
      this.fetchReverseCity(lat, lon);
    }
  },
  components: {
    CardComponent,
    SettingsPage,
  },
};
</script>

<template>
  <div class="container">
    <div v-if="json">
      <CardComponent :data="json" :title="title"/>
    </div>
    <SettingsPage :settings="settings" :on-change="onSettingsChange"/>
  </div>
</template>

<style scoped></style>
