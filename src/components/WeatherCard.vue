<template>
  <div class="weather_card" :style="{ backgroundImage: `url(${backgroundImageUrl})` }">
    <div class="card-header">
      <div class="weather_board__card__location_title">
        <p>{{ city }}
          <img :src="flagUrl" alt="Flag" class="flag" width="20" height="15"/>
        </p>
      </div>
      <div class="unit-switch">
        <label>
          <input type="radio" name="unit-system" value="metric" v-model="unitSystem">
          Metric
        </label>
        <label>
          <input type="radio" name="unit-system" value="imperial" v-model="unitSystem">
          Imperial
        </label>
      </div>
    </div><br>
    <div class="card-body">
      <h2 class="today">Today's Weather</h2>
      <div class="weather-details">
        <div class="weather-info">
          <div class="weather-description">{{ weatherDescription }}</div>
          <div class="weather-temperature">{{ temperature }}{{ temperatureUnit }}</div>
          <div class="weather-icon">
            <img :src="getWeatherIconUrl(weatherIconCode)" alt="Weather Icon">
          </div>
        </div>
      </div><br><br>
      <h2 class="tomorrow">Tomorrow's Weather</h2>
      <div class="weather-details">
        <div class="weather-info">
          <div class="weather-description">{{ tomorrowWeatherDescription }}</div>
          <div class="weather-temperature">{{ tomorrowTemperature }}{{ temperatureUnit }}</div>
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
      unitSystem: 'metric',
    }
  },
  computed: {
    backgroundImageUrl() {
      const backgroundImageUrls = {
        'clear sky': 'assets/img/weather/clear.jpg',
        'few clouds': 'assets/img/weather/clouds.jpg',
        'overcast clouds': 'assets/img/weather/clouds.jpg',
        'scattered clouds': 'assets/img/weather/clouds.jpg',
        'broken clouds': 'assets/img/weather/clouds.jpg',
        'shower rain': 'assets/img/weather/rain.jpg',
        'rainy': 'assets/img/weather/rain.jpg',
        'thunderstorm': 'assets/img/weather/thunderstorm.jpg',
        'snowy': 'assets/img/weather/snow.jpg',
        'light snow': 'assets/img/weather/snow.jpg',
        'mist': 'assets/img/weather/mist.jpg',
        'light rain': 'assets/img/weather/drizzle.jpg',
      };
      return backgroundImageUrls[this.weatherDescription?.toLowerCase()] || '';
    },
    weatherDescription() {
      return this.weather?.weather[0]?.description
    },
    temperature() {
      if (this.unitSystem === 'imperial') {
        return Math.round(this.weather?.main?.temp * 9 / 5 + 32)
      } else {
        return Math.round(this.weather?.main?.temp)
      }
    },
    temperatureUnit() {
      return this.unitSystem === 'imperial' ? '°F' : '°C'
    },
    weatherIconCode() {
      return this.weather?.weather[0]?.icon
    },
    tomorrowWeatherDescription() {
      return this.tomorrowWeather?.list[0]?.weather[0]?.description
    },
    tomorrowTemperature() {
      if (this.unitSystem === 'imperial') {
        return Math.round(this.tomorrowWeather?.list[0]?.main?.temp * 9 / 5 + 32)
      } else {
        return Math.round(this.tomorrowWeather?.list[0]?.main?.temp)
      }
    },
    tomorrowWeatherIconCode() {
      return this.tomorrowWeather?.list[0]?.weather[0]?.icon
    },
    flagUrl() {
      const countryCode = this.weather?.sys?.country || '';
      return `https://flagcdn.com/20x15/${countryCode.toLowerCase()}.png`;
    },
  },
  methods: {
    async fetchWeatherData() {
      const apiKey = 'f18e79e75665bd4bd01164484ad7306b'
      const units = this.unitSystem === 'metric' ? 'metric' : 'imperial'
      const todayUrl = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${apiKey}&units=${units}`
      const tomorrowUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&appid=${apiKey}&units=${units}`
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
.weather_card {
  margin-bottom: 20px;
  border: 1px solid #ddd;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 20px;
  color: white;
}
.flag {
  margin-right: 10px;
  vertical-align: middle;
}

.unit-switch {
  display: flex;
  justify-content: space-around;
}

.weather-details{
  display: flex;
  justify-content: space-around;
}

.today{
  display: flex;
  justify-content: center;
}

.tomorrow{
  display: flex;
  justify-content: center;
}
/* Title */
.weather_board__card__location_title {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  margin: 0 auto;
}

.weather_board__card__location_title p {
  margin-right: 5px;
  font-weight: 400;
  font-size: 18px;
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
  color: white;
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
