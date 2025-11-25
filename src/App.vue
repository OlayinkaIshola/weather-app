<template>
<div id="app" :class="getWeatherClass()">
  <!-- Weather animations -->
  <div class="weather-animation">
    <div v-if="isSnowing" class="snowflakes">
      <div class="snowflake" v-for="i in 50" :key="i"></div>
    </div>
    <div v-if="isRaining" class="raindrops">
      <div class="raindrop" v-for="i in 100" :key="i"></div>
    </div>
    <div v-if="isCloudy" class="clouds">
      <div class="cloud cloud1"></div>
      <div class="cloud cloud2"></div>
      <div class="cloud cloud3"></div>
    </div>
  </div>

  <main>
    <div class="search-box">
      <input
        type="text"
        class="search-bar"
        placeholder="🔍 Search for a city..."
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>

    <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
      <div class="location-box">
        <div class="weather-icon">{{ getWeatherEmoji() }}</div>
        <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
        <div class="date">{{ dateBulider() }}</div>
      </div>

      <div class="weather-box">
        <div class="temp-container">
          <div class="temp">{{ Math.round(weather.main.temp) }}°C</div>
          <div class="weather-description">{{ weather.weather[0].main }}</div>
        </div>

        <div class="weather-details">
          <div class="detail-item">
            <span class="detail-label">Feels Like</span>
            <span class="detail-value">{{ Math.round(weather.main.feels_like) }}°C</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">Humidity</span>
            <span class="detail-value">{{ weather.main.humidity }}%</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">Wind Speed</span>
            <span class="detail-value">{{ Math.round(weather.wind.speed) }} m/s</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">Pressure</span>
            <span class="detail-value">{{ weather.main.pressure }} hPa</span>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="welcome-message">
      <div class="welcome-icon">🌤️</div>
      <h1>Weather App</h1>
      <p>Search for a city to see the weather</p>
    </div>
  </main>
</div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      api_key: '49b0a0c5ac5c6a4f06746e79ed6bf396',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
    }
  },
  computed: {
    isSnowing() {
      return this.weather.weather && this.weather.weather[0].main.toLowerCase().includes('snow');
    },
    isRaining() {
      return this.weather.weather && this.weather.weather[0].main.toLowerCase().includes('rain');
    },
    isCloudy() {
      return this.weather.weather && this.weather.weather[0].main.toLowerCase().includes('cloud');
    },
  },
  methods: {
    fetchWeather(e) {
      if (e.key === 'Enter') {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => res.json())
          .then(this.setResults)
          .catch(error => console.error('Error fetching weather:', error));
      }
    },
    setResults(results) {
      this.weather = results;
    },
    getWeatherClass() {
      if (!this.weather.weather) return 'default-bg';

      const weatherType = this.weather.weather[0].main.toLowerCase();

      if (weatherType.includes('snow')) return 'snow-bg';
      if (weatherType.includes('rain')) return 'rain-bg';
      if (weatherType.includes('cloud')) return 'cloudy-bg';
      if (weatherType.includes('clear') || weatherType.includes('sunny')) return 'sunny-bg';
      if (weatherType.includes('thunderstorm')) return 'storm-bg';
      if (weatherType.includes('mist') || weatherType.includes('fog')) return 'fog-bg';

      return this.weather.main.temp > 16 ? 'warm-bg' : 'cold-bg';
    },
    getWeatherEmoji() {
      if (!this.weather.weather) return '🌤️';

      const weatherType = this.weather.weather[0].main.toLowerCase();

      if (weatherType.includes('snow')) return '❄️';
      if (weatherType.includes('rain')) return '🌧️';
      if (weatherType.includes('cloud')) return '☁️';
      if (weatherType.includes('clear')) return '☀️';
      if (weatherType.includes('sunny')) return '🌞';
      if (weatherType.includes('thunderstorm')) return '⛈️';
      if (weatherType.includes('mist') || weatherType.includes('fog')) return '🌫️';
      if (weatherType.includes('drizzle')) return '🌦️';

      return '🌤️';
    },
    dateBulider() {
      let d = new Date();
      let months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
      let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>

<style scoped>
/* ===== ANIMATIONS ===== */
@keyframes snowfall {
  0% {
    transform: translateY(-10vh) translateX(0);
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    transform: translateY(100vh) translateX(100px);
    opacity: 0;
  }
}

@keyframes rainfall {
  0% {
    transform: translateY(-10vh);
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    transform: translateY(100vh);
    opacity: 0;
  }
}

@keyframes cloudFloat {
  0%, 100% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(30px);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

@keyframes glow {
  0%, 100% {
    box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
  }
  50% {
    box-shadow: 0 0 40px rgba(255, 255, 255, 0.6);
  }
}

/* ===== BASE STYLES ===== */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

#app {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  transition: all 1s ease-in-out;
}

/* ===== WEATHER BACKGROUNDS ===== */
#app.default-bg {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

#app.sunny-bg {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

#app.cloudy-bg {
  background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
}

#app.rain-bg {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

#app.snow-bg {
  background: linear-gradient(135deg, #e0c3fc 0%, #8ec5fc 100%);
}

#app.storm-bg {
  background: linear-gradient(135deg, #2c3e50 0%, #34495e 100%);
}

#app.fog-bg {
  background: linear-gradient(135deg, #bdc3c7 0%, #95a5a6 100%);
}

#app.warm-bg {
  background: linear-gradient(135deg, #ff9a56 0%, #ff6a88 100%);
}

#app.cold-bg {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

/* ===== WEATHER ANIMATIONS CONTAINER ===== */
.weather-animation {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}

/* ===== SNOWFLAKES ===== */
.snowflakes {
  position: absolute;
  width: 100%;
  height: 100%;
}

.snowflake {
  position: absolute;
  width: 10px;
  height: 10px;
  background: white;
  border-radius: 50%;
  opacity: 0.8;
  animation: snowfall linear infinite;
  box-shadow: 0 0 10px rgba(255, 255, 255, 0.8);
}

.snowflake:nth-child(1) { left: 10%; animation-duration: 10s; animation-delay: 0s; }
.snowflake:nth-child(2) { left: 20%; animation-duration: 12s; animation-delay: 1s; }
.snowflake:nth-child(3) { left: 30%; animation-duration: 14s; animation-delay: 2s; }
.snowflake:nth-child(4) { left: 40%; animation-duration: 16s; animation-delay: 3s; }
.snowflake:nth-child(5) { left: 50%; animation-duration: 18s; animation-delay: 4s; }
.snowflake:nth-child(6) { left: 60%; animation-duration: 20s; animation-delay: 5s; }
.snowflake:nth-child(7) { left: 70%; animation-duration: 22s; animation-delay: 6s; }
.snowflake:nth-child(8) { left: 80%; animation-duration: 24s; animation-delay: 7s; }
.snowflake:nth-child(9) { left: 90%; animation-duration: 26s; animation-delay: 8s; }
.snowflake:nth-child(10) { left: 5%; animation-duration: 28s; animation-delay: 9s; }

.snowflake:nth-child(n+11) { left: calc(10% * (var(--i, 1))); }

/* ===== RAINDROPS ===== */
.raindrops {
  position: absolute;
  width: 100%;
  height: 100%;
}

.raindrop {
  position: absolute;
  width: 2px;
  height: 20px;
  background: linear-gradient(to bottom, rgba(255, 255, 255, 0.8), rgba(255, 255, 255, 0));
  animation: rainfall linear infinite;
  opacity: 0.6;
}

.raindrop:nth-child(1) { left: 5%; animation-duration: 0.5s; animation-delay: 0s; }
.raindrop:nth-child(2) { left: 10%; animation-duration: 0.6s; animation-delay: 0.1s; }
.raindrop:nth-child(3) { left: 15%; animation-duration: 0.5s; animation-delay: 0.2s; }
.raindrop:nth-child(4) { left: 20%; animation-duration: 0.7s; animation-delay: 0.3s; }
.raindrop:nth-child(5) { left: 25%; animation-duration: 0.6s; animation-delay: 0.4s; }
.raindrop:nth-child(n+6) { left: calc(5% * (var(--i, 1))); }

/* ===== CLOUDS ===== */
.clouds {
  position: absolute;
  width: 100%;
  height: 100%;
}

.cloud {
  position: absolute;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 100px;
  opacity: 0.5;
  animation: cloudFloat 20s ease-in-out infinite;
}

.cloud1 {
  width: 200px;
  height: 60px;
  top: 20%;
  left: 10%;
  animation-duration: 20s;
}

.cloud2 {
  width: 150px;
  height: 50px;
  top: 40%;
  right: 15%;
  animation-duration: 25s;
  animation-delay: 5s;
}

.cloud3 {
  width: 180px;
  height: 55px;
  top: 60%;
  left: 20%;
  animation-duration: 30s;
  animation-delay: 10s;
}

/* ===== MAIN CONTENT ===== */
main {
  position: relative;
  z-index: 2;
  min-height: 100vh;
  padding: 40px 25px;
  background: linear-gradient(to bottom, rgba(0, 0, 0, 0.2), rgba(0, 0, 0, 0.4));
  display: flex;
  flex-direction: column;
}

/* ===== SEARCH BOX ===== */
.search-box {
  width: 100%;
  max-width: 500px;
  margin: 0 auto 50px;
  animation: fadeIn 0.8s ease-out;
}

.search-bar {
  display: block;
  width: 100%;
  padding: 18px 25px;
  color: #333;
  font-size: 18px;
  appearance: none;
  border: none;
  outline: none;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(4px);
  transition: all 0.4s ease;
  font-weight: 500;
}

.search-bar::placeholder {
  color: rgba(255, 255, 255, 0.7);
}

.search-bar:focus {
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  transform: translateY(-2px);
}

/* ===== WEATHER WRAP ===== */
.weather-wrap {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  animation: fadeIn 1s ease-out;
}

/* ===== LOCATION BOX ===== */
.location-box {
  text-align: center;
  margin-bottom: 40px;
}

.weather-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: pulse 2s ease-in-out infinite;
}

.location {
  color: #fff;
  font-size: 42px;
  font-weight: 700;
  text-align: center;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.3);
  margin-bottom: 10px;
  letter-spacing: 1px;
}

.date {
  color: rgba(255, 255, 255, 0.9);
  font-size: 18px;
  font-weight: 300;
  text-align: center;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.2);
}

/* ===== WEATHER BOX ===== */
.weather-box {
  text-align: center;
  width: 100%;
  max-width: 600px;
}

.temp-container {
  margin-bottom: 40px;
  animation: glow 3s ease-in-out infinite;
}

.temp {
  display: inline-block;
  padding: 30px 60px;
  color: #fff;
  font-size: 120px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.3);
  background-color: rgba(255, 255, 255, 0.15);
  border-radius: 30px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(4px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  margin-bottom: 15px;
}

.weather-description {
  color: rgba(255, 255, 255, 0.95);
  font-size: 32px;
  font-weight: 600;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.3);
  letter-spacing: 1px;
}

/* ===== WEATHER DETAILS ===== */
.weather-details {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 20px;
  margin-top: 40px;
}

.detail-item {
  background-color: rgba(255, 255, 255, 0.1);
  padding: 20px;
  border-radius: 20px;
  backdrop-filter: blur(4px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
  animation: fadeIn 1s ease-out;
}

.detail-item:hover {
  background-color: rgba(255, 255, 255, 0.2);
  transform: translateY(-5px);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
}

.detail-label {
  display: block;
  color: rgba(255, 255, 255, 0.8);
  font-size: 12px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 8px;
}

.detail-value {
  display: block;
  color: #fff;
  font-size: 24px;
  font-weight: 700;
}

/* ===== WELCOME MESSAGE ===== */
.welcome-message {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  animation: fadeIn 1s ease-out;
}

.welcome-icon {
  font-size: 100px;
  margin-bottom: 30px;
  animation: pulse 2s ease-in-out infinite;
}

.welcome-message h1 {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  margin-bottom: 15px;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.3);
}

.welcome-message p {
  color: rgba(255, 255, 255, 0.9);
  font-size: 20px;
  font-weight: 300;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.2);
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  main {
    padding: 30px 15px;
  }

  .location {
    font-size: 32px;
  }

  .temp {
    font-size: 80px;
    padding: 20px 40px;
  }

  .weather-description {
    font-size: 24px;
  }

  .weather-details {
    grid-template-columns: repeat(2, 1fr);
  }

  .welcome-message h1 {
    font-size: 36px;
  }

  .welcome-message p {
    font-size: 16px;
  }
}

@media (max-width: 480px) {
  .location {
    font-size: 24px;
  }

  .temp {
    font-size: 60px;
    padding: 15px 30px;
  }

  .weather-description {
    font-size: 20px;
  }

  .weather-details {
    grid-template-columns: 1fr;
  }

  .weather-icon {
    font-size: 60px;
  }

  .welcome-icon {
    font-size: 80px;
  }
}
</style>
