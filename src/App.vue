<!-- eslint-disable no-unused-vars -->
<script>
import CardComponent from "./components/WeatherCard.vue";
import SettingsPage from "./pages/SettingsPage.vue";

import API_KEY from "./constants";

export default {
  data() {
    return {
      title: "",
      data: [],
      settings: [],
      openSettings: false
    };
  },
  methods: {
    async fetchReverseLocation(lat, lon) {
      const res = await fetch(
        `https://api.openweathermap.org/geo/1.0/reverse?lat=${lat}&lon=${lon}&limit=5&appid=${API_KEY}`
      ).then((data) => data.json());

      const title = `${res[0].name}, ${res[0].country}`;

      
     const data = await this.fetchData(res[0].lat.toFixed(2), res[0].lon.toFixed(2));

     this.data.push({id: this.data.length, data: {title: title, ...data}})

    },
    async fetchData(lat, lon) {
      const res = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${API_KEY}`
      ).then((res) => res.json());

      return res;
    },
    parseSettings() {
      this.settings.forEach((item) => {
        this.fetchReverseLocation(item.location.lat, item.location.lon);
      });
    },
    onSettingsChange(newSettings) {

      this.data = [];
      this.settings = [...newSettings];
      window.localStorage.setItem("settings", JSON.stringify(newSettings));
      this.toggleSettings();
      this.parseSettings();
    },
    toggleSettings () {
      this.openSettings = !this.openSettings
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
        JSON.stringify([{ id: 0, location: currentPosition }])
      );
      this.settings = JSON.parse(localStorage);
      this.fetchReverseLocation(currentPosition.lat, currentPosition.lon);
    };

    const errorCallback = (error) => {
      console.log(error);
    };

    const localStorage = window.localStorage.getItem("settings");

    if (!localStorage) {
      navigator.geolocation.getCurrentPosition(successCallback, errorCallback);
    } else {
      this.settings = JSON.parse(localStorage);
      this.parseSettings();
    }
  },
  components: {
    CardComponent,
    SettingsPage,
  },
};
</script>

<template>
  <div class="container relative p-2">
    <q-btn class="absolute-top-right z-top" round flat icon="settings" @click="toggleSettings"/>
    <div v-if="data.length > 0">
      <CardComponent
        v-for="item in data"
        :key="item.id"
        :data="item.data"
        
      />
    </div>
    <div v-else>
      <CardComponent        
      />
    </div>
    <SettingsPage v-if="openSettings" :settings="settings" :on-change="onSettingsChange" />
  </div>
</template>
