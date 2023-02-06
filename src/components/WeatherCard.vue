<script>
export default {
    props: [
        "data"
    ]
}
</script>


<template>
    <div class="card">
      <div class="card-title">
        <span class="bold-text">{{data.title}}</span>
      </div>
      <div class="card-content">
        <div class="weather">
          <img class="weather-icon" :src="`http://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`"/>
          <div class="weather-temp">
            <span class="heading">{{ data.main.temp+"&deg;C"}}</span>
          </div>
        </div>
        <div class="temp-description">
          <p>{{ `Feels like: ${data.main.feels_like}&deg;C. ${data.weather[0].description.charAt(0).toUpperCase()+data.weather[0].description.slice(1)}.` }}</p>
        </div>
        <div class="info">
          <div class="info-item">
            Wind: <span>{{data.wind.speed}} m/s </span>
          </div>
          <div class="info-item">Pressure: <span>{{ data.main.pressure }}hPa</span></div>
          <div class="info-item">Humidity: <span>{{ data.main.humidity }}%</span></div>
          <div class="info-item">Dew point: <span>{{ data.main.pressure }}</span></div>
          <div class="info-item">Visibility: <span>{{ data.visibility }} meters</span></div>
        </div>
      </div>
    </div>
  </template>

<style>

.heading {
  font-size: 2rem;
}

.bold-text {
  font-weight: 600;
}

.temp-description{
  padding-left: 0.5rem;
}

.card {
  min-width: 4rem;
  max-width: 20rem;
  padding: 1rem;
}

.card-title {
  padding-left: .5rem;
}

.weather {
  display: flex;
  flex-direction: row;
}

.weather-icon {
  width: 50px;
  height: 50px;
}


.info {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: space-between;
  align-items: start;
  align-content: start
}
.info-item {
  flex: 0 0 auto;
  margin: 0.5rem;
}
</style>