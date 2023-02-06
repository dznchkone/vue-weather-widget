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
    startDrag(evt, item) {
      evt.dataTransfer.dropEffect = "move";
      evt.dataTransfer.effectAllowed = "move";
      evt.dataTransfer.setData("itemID", item.id);
    },
    onDrop(evt, elID) {
      const itemID = evt.dataTransfer.getData("itemID");
      const item = this.currentCities.find((item) => item.id == itemID);

      const temp = this.currentCities.find((item) => item.id == elID);

      console.log(item, temp);

      this.currentCities = [...this.currentCities.map(city=>{
        if (city.id === temp.id) return ({id:city.id, ...item})
        if (city.id === item.id) return ({id:city.id, ...temp})
        return city
      })]
    },
    fetchLocationByName(text) {
      if (text.length > 0) {
        fetch(
          `http://api.openweathermap.org/geo/1.0/direct?q=${text}&limit=5&appid=${API_KEY}`
        )
          .then((data) => data.json())
          .then((json) => {
            this.searchResult = json;
          });
      } else {
        this.searchResult = null;
      }
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
    handleDelete(id) {
      this.currentCities = this.currentCities.filter((item) => item.id !== id);
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
  <q-dialog v-model="open" persistent class="z-max">
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
          
          <q-item
            class="row justify-between items-center shadow-1"
            v-for="city in currentCities"
            :key="city.id"
            draggable="true" 
            v-on:dragstart="startDrag($event, city)"
            v-on:drop="onDrop($event,city.id)"
            @dragover.prevent
            @dragenter.prevent
          >
          <span  >
            <q-icon name="menu"></q-icon>
          </span>
            <span>{{ city.title }}</span>
            <q-btn
              icon="delete"
              flat
              dense
              @click="handleDelete(city.id)"
            ></q-btn>
          </q-item>
        </q-list>
      </q-card-section>

      <q-card-section class="col">
        <span>Add Location:</span>
        <div class="row">
          <q-input color="primary" outlined v-model="searchText"> </q-input>
          <q-btn
            icon="search"
            outline
            color="primary"
            @click="fetchLocationByName(this.searchText)"
          />
        </div>

        <q-space />

        <q-list v-if="searchResult">
          <q-item
            clickable
            class="row justify-between items-center shadow-1"
            v-for="result in searchResult"
            :key="result.lat"
            @click="(event) => this.handleClickListItem(result)"
          >
            {{ `${result.name}, ${result.state},  ${result.country}` }}
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
