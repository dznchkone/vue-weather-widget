<script>
import API_KEY from "../constants";

export default {
    props: ["settings", "onChange"],
  data() {
    return {
      searchText: "",
      debounce: null,
      searchResult: null,
      currentCities:[],
    };
  },
  methods: {
    debounceSearch(event) {
      clearTimeout(this.debounce);
      if (event.target.value.length > 0) {
        this.debounce = setTimeout(() => {
          this.searchText = event.target.value;
          this.fetchCityByName(event.target.value);
        }, 500);
      }
    },
    fetchCityByName(text) {
      fetch(
        `http://api.openweathermap.org/geo/1.0/direct?q=${text}&limit=5&appid=${API_KEY}`
      )
        .then((data) => data.json())
        .then((json) => {
          this.searchResult = json;
        });
    },
    handleClick (data) {
       this.searchResult = null;
       this.currentCities.push({id:this.currentCities.length, lacation: {lat:data.lat, lon: data.lon}, title: `${data.name}, ${data.country}`});
    },

  },
  mounted () {
        this.settings.forEach(item=>{
            console.log(item);
            fetch(
        `http://api.openweathermap.org/geo/1.0/reverse?lat=${item.location.lat}&lon=${item.location.lon}&limit=5&appid=${API_KEY}`
      )
        .then((data) => data.json())
        .then((json) => {
          this.currentCities.push({id: this.currentCities.length, title: `${json[0].name}, ${json[0].country}`})
        });
        })
  }
};
</script>

<template>
  <div>
    <div>
      <span>Settings</span>
      <span>Close Icon</span>
    </div>
    <ul v-if="currentCities.length> 0">
      <li v-for="city in currentCities" :key="city.id">
        <span>burger</span>
        <span>{{ city.title }}</span>
        <span>deleteIcon</span>
      </li>
    </ul>

    <div>
      <span>Add Location</span>
      <input :value="searchText" @input="debounceSearch" /><span>AddIcon</span>
      <ul v-if="searchResult">
        <li v-for="result in searchResult" :key="result.lat" @click="handleClick(result)">
          {{ `${result.name}, ${result.state},  ${result.country}` }}
        </li>
      </ul>
    </div>
  </div>
</template>
