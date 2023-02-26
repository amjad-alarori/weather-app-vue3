<template>
  <div class="card">
    <div class="card-header">
      <h3>{{ city }}</h3>
    </div>
    <div class="card-body">
      <div class="weather-details">
        <div class="weather-info">
          <div class="weather-description">{{ weatherDescription }}</div>
          <div class="weather-temperature">{{ temperature }}°C</div>
          <div class="weather-icon">
            <img :src="getWeatherIconUrl(weatherIconCode)" alt="Weather Icon">
          </div>
        </div>
      </div>
      <div class="weather-details">
        <div class="weather-info">
          <div class="weather-description">{{ tomorrowWeatherDescription }}</div>
          <div class="weather-temperature">{{ tomorrowTemperature }}°C</div>
          <div class="weather-icon">
            <img :src="getWeatherIconUrl(tomorrowWeatherIconCode)" alt="Weather Icon">
          </div>
        </div>
      </div>
    </div>
    <div class="card-footer">
      <button class="btn btn-danger" @click="$emit('remove')">Remove</button>
    </div>
  </div>
</template>


<script>

export default {
  props: {
    city: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      weather: null,
      tomorrowWeather: null,
    }
  },
  computed: {
    weatherDescription() {
      return this.weather?.weather[0]?.description
    },
    temperature() {
      return Math.round(this.weather?.main?.temp)
    },
    weatherIconCode() {
      return this.weather?.weather[0]?.icon
    },
    tomorrowWeatherDescription() {
      return this.tomorrowWeather?.list[0]?.weather[0]?.description
    },
    tomorrowTemperature() {
      return Math.round(this.tomorrowWeather?.list[0]?.main?.temp)
    },
    tomorrowWeatherIconCode() {
      return this.tomorrowWeather?.list[0]?.weather[0]?.icon
    },
  },
  methods: {
    async fetchWeatherData() {
      const apiKey = 'f18e79e75665bd4bd01164484ad7306b'
      const todayUrl = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${apiKey}&units=metric`
      const tomorrowUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&appid=${apiKey}&units=metric`
      const [todayResponse, tomorrowResponse] = await Promise.all([
        fetch(todayUrl),
        fetch(tomorrowUrl),
      ])
      this.weather = await todayResponse.json()
      this.tomorrowWeather = await tomorrowResponse.json()
    },
    getWeatherIconUrl(iconCode) {
      return `https://openweathermap.org/img/wn/${iconCode}.png`
    },
  },
  mounted() {
    this.fetchWeatherData()
  },
}

</script>

<style>
.card {
  margin-bottom: 20px;
  border: 1px solid #ddd;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}

.card-header {
  padding: 12px 20px;
  font-size: 24px;
  font-weight: bold;
  background-color: #f7f7f7;
  border-bottom: 1px solid #ddd;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}

.card-body {
  padding: 20px;
}


.weather-description {
  font-size: 20px;
  margin-bottom: 10px;
  color: #666;
}

.weather-temperature {
  font-size: 32px;
  font-weight: bold;
}

.card-footer {
  padding: 12px 20px;
  background-color: #f7f7f7;
  border-top: 1px solid #ddd;
  border-bottom-left-radius: 8px;
  border-bottom-right-radius: 8px;
  text-align: right;
}

.btn-danger {
  background-color: #dc3545;
  color: #fff;
}

.btn-danger:hover {
  background-color: #c82333;
}
</style>