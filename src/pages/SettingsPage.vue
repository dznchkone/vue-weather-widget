<script>
import API_KEY from "../constants";

export default {
  props: ["settings", "onChange"],
  data() {
    return {
      searchText: "",
      debounce: null,
      searchResult: null,
      currentCities: [],
      open: true,
    };
  },
  methods: {
    debounceSearch(event) {
      clearTimeout(this.debounce);
      if (event.target.value.length > 0) {
        this.debounce = setTimeout(() => {
          this.searchText = event.target.value;
          this.fetchLocationByName(event.target.value);
        }, 500);
      }
    },
    fetchLocationByName(text) {
      fetch(
        `http://api.openweathermap.org/geo/1.0/direct?q=${text}&limit=5&appid=${API_KEY}`
      )
        .then((data) => data.json())
        .then((json) => {
          this.searchResult = json;
        });
    },
    handleClickListItem(data) {
      this.searchResult = null;
      this.currentCities.push({
        id: this.currentCities.length,
        location: { lat: data.lat.toFixed(2), lon: data.lon.toFixed(2) },
        title: `${data.name}, ${data.country}`,
      });
    },
    handleCloseSettings() {
      const newSettings = [
        ...this.currentCities.map((item) => ({
          id: item.id,
          location: { ...item.location },
        })),
      ];
      this.onChange(newSettings);
    },
  },
  created() {
    this.settings.forEach((item) => {
      fetch(
        `http://api.openweathermap.org/geo/1.0/reverse?lat=${item.location.lat}&lon=${item.location.lon}&limit=5&appid=${API_KEY}`
      )
        .then((data) => data.json())
        .then((json) => {
          this.currentCities.push({
            id: this.currentCities.length,
            title: `${json[0].name}, ${json[0].country}`,
            location: item.location,
          });
        });
    });
  },
};
</script>

<template>
  <q-dialog v-model="open">
    <q-card class="column">
      <q-card-section class="row items-center q-pb-none">
        <div class="text-h6">Settings</div>
        <q-space />
        <q-btn
          icon="close"
          flat
          round
          dense
          v-close-popup
          @click="handleCloseSettings"
        />
      </q-card-section>
      <q-card-section>
        <q-list v-if="currentCities.length > 0">
          <q-item v-for="city in currentCities" :key="city.id">
            <q-icon name="menu"></q-icon>
            <span>{{ city.title }}</span>
            <q-btn icon="delete"
            flat 
            dense></q-btn>
          </q-item>
        </q-list>
      </q-card-section>

      <q-card-section class="col">
        <span>Add Location:</span>
        <div>
          <input :value="searchText" @input="debounceSearch" />
          <q-btn icon="search" flat dense />
        </div>

        <q-input color="primary" outlined  v-model="searchText">

        </q-input>

        <ul v-if="searchResult">
          <li
            v-for="result in searchResult"
            :key="result.lat"
            @click="handleClickListItem(result)"
          >
            {{ `${result.name}, ${result.state},  ${result.country}` }}
          </li>
        </ul>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
