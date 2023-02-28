<template>
  <div class="container py-5">
    <h1 class="text-center mb-5">Weather App</h1>
    <div class="row">
      <div class="col-lg-6 mx-auto mb-3">
        <form @submit.prevent="addCity">
          <div class="input-group">
            <input type="text" class="form-control" id="cityInput" v-model="newCity" placeholder="Enter a city name">
            <button type="submit" class="btn btn-primary">Add</button>
          </div>
        </form>
      </div>
    </div><br>
    <div class="row">
      <div class="col-lg-10 mx-auto">
        <div class="row">
          <WeatherCard v-for="(city, index) in cities" :key="index" :city="city" @remove="removeCity(index)" class="col-md-4 mb-4" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import WeatherCard from './components/WeatherCard.vue'

export default {
  components: {
    WeatherCard,
  },
  data() {
    return {
      cities: ['Amsterdam'],
      newCity: '',
    }
  },
  methods: {
    addCity() {
      if (this.newCity && !this.cities.includes(this.newCity)) {
        this.cities.push(this.newCity)
        this.newCity = ''
      }
    },
    removeCity(index) {
      this.cities.splice(index, 1)
    },
  },
}
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Montserrat:200,300,400,500,600,700,800&display=swap");

.container {
  font-family: "Montserrat", sans-serif;
  line-height: 1.4;
}
</style>
