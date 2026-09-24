<template>
  <div class="app-container">
    <header class="app-header">
      <h1>🌤️ Météo App</h1>
      <p class="subtitle">Recherchez la météo d'une ville dans le monde</p>
    </header>

    <SearchBar @search="onSearch" />

    <!-- Sélecteur °C / °F -->
    <div class="unit-selector" v-show="weather">
      <label>
        <input type="radio" value="celsius" v-model="unit" /> °C
      </label>
      <label>
        <input type="radio" value="fahrenheit" v-model="unit" /> °F
      </label>
    </div>

    <!-- État : chargement -->
    <LoadingMessage :visible="loading" />

    <!-- État : erreur -->
    <div v-if="error" class="error-box">
      <p>⚠️ {{ error }}</p>
    </div>

    <!-- État : succès -->
    <WeatherCard
      v-if="!loading && !error && weather"
      :weather="weather"
      :unit="unit"
    />

    <!-- État initial -->
    <div v-if="!loading && !error && !weather && !searched" class="welcome-box">
      <p>🔎 Entrez le nom d'une ville pour commencer.</p>
    </div>

    <!-- Historique -->
    <div v-if="history.length > 0" class="history-box">
      <h3>🕘 Historique des recherches</h3>
      <ul>
        <li v-for="(item, index) in history" :key="index" @click="onSearch(item)">
          {{ item }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import SearchBar from './components/SearchBar.vue'
import WeatherCard from './components/WeatherCard.vue'
import LoadingMessage from './components/LoadingMessage.vue'

const WEATHER_CODES = {
  0:  { label: 'Ciel dégagé',            icon: '☀️' },
  1:  { label: 'Principalement dégagé',  icon: '🌤️' },
  2:  { label: 'Partiellement nuageux',  icon: '⛅' },
  3:  { label: 'Couvert',                icon: '☁️' },
  45: { label: 'Brouillard',             icon: '🌫️' },
  48: { label: 'Brouillard givrant',     icon: '🌫️' },
  51: { label: 'Bruine légère',          icon: '🌦️' },
  53: { label: 'Bruine modérée',         icon: '🌦️' },
  55: { label: 'Bruine dense',           icon: '🌧️' },
  61: { label: 'Pluie légère',           icon: '🌧️' },
  63: { label: 'Pluie modérée',          icon: '🌧️' },
  65: { label: 'Pluie forte',            icon: '🌧️' },
  71: { label: 'Neige légère',           icon: '🌨️' },
  73: { label: 'Neige modérée',          icon: '🌨️' },
  75: { label: 'Neige forte',            icon: '❄️' },
  80: { label: 'Averses légères',        icon: '🌦️' },
  81: { label: 'Averses modérées',       icon: '🌧️' },
  82: { label: 'Averses violentes',      icon: '⛈️' },
  95: { label: 'Orage',                  icon: '⛈️' },
  96: { label: 'Orage avec grêle',       icon: '⛈️' },
  99: { label: 'Orage violent',          icon: '⛈️' }
}

export default {
  name: 'App',
  components: { SearchBar, WeatherCard, LoadingMessage },
  data() {
    return {
      city: '',
      weather: null,
      loading: false,
      error: '',
      searched: false,
      history: [],
      unit: 'celsius'
    }
  },
  methods: {
    async fetchCoordinates(city) {
      const url = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(
        city
      )}&count=1&language=fr&format=json`
      const res = await fetch(url)
      if (!res.ok) throw new Error('network')
      const data = await res.json()
      if (!data.results || data.results.length === 0) {
        throw new Error('notfound')
      }
      return data.results[0]
    },

    async fetchWeather(lat, lon) {
      const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,wind_speed_10m,weather_code&timezone=auto`
      const res = await fetch(url)
      if (!res.ok) throw new Error('network')
      return await res.json()
    },

    async onSearch(cityName) {
      this.loading = true
      this.error = ''
      this.weather = null
      this.searched = true

      try {
        const location = await this.fetchCoordinates(cityName)
        const data = await this.fetchWeather(location.latitude, location.longitude)
        const current = data.current
        const code = current.weather_code
        const info = WEATHER_CODES[code] || { label: 'Inconnu', icon: '❓' }

        this.weather = {
          city: location.name,
          country: location.country || '',
          temperature: Math.round(current.temperature_2m),
          humidity: current.relative_humidity_2m,
          windSpeed: current.wind_speed_10m,
          condition: info.label,
          iconUrl: `https://openweathermap.org/img/wn/${this.mapIcon(code)}@2x.png`,
          icon: info.icon,
          code
        }

        if (!this.history.includes(location.name)) {
          this.history.unshift(location.name)
          if (this.history.length > 5) this.history.pop()
        }
      } catch (err) {
        if (err.message === 'notfound') {
          this.error = `Ville "${cityName}" introuvable. Vérifiez l'orthographe.`
        } else {
          this.error = 'Erreur réseau ou API. Veuillez réessayer plus tard.'
        }
      } finally {
        this.loading = false
      }
    },

    mapIcon(code) {
      if (code === 0) return '01d'
      if (code <= 2) return '02d'
      if (code === 3) return '04d'
      if (code <= 48) return '50d'
      if (code <= 55) return '09d'
      if (code <= 65) return '10d'
      if (code <= 75) return '13d'
      if (code <= 82) return '09d'
      return '11d'
    }
  }
}
</script>

<style scoped>
.app-container {
  width: 100%;
}

.app-header {
  text-align: center;
  margin-bottom: 25px;
  color: #fff;
}

.app-header h1 {
  font-size: 2.2rem;
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

.subtitle {
  font-size: 1rem;
  opacity: 0.9;
  margin-top: 5px;
}

.unit-selector {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 15px;
  color: #fff;
  font-weight: 600;
}

.unit-selector label {
  cursor: pointer;
  background: rgba(255, 255, 255, 0.25);
  padding: 6px 14px;
  border-radius: 20px;
}

.error-box {
  background: #ffe0e0;
  color: #b00020;
  padding: 20px;
  border-radius: 12px;
  text-align: center;
  font-weight: 600;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.1);
}

.welcome-box {
  background: rgba(255, 255, 255, 0.85);
  padding: 30px;
  border-radius: 16px;
  text-align: center;
  font-size: 1.05rem;
  color: #555;
}

.history-box {
  margin-top: 25px;
  background: rgba(255, 255, 255, 0.85);
  padding: 18px;
  border-radius: 16px;
}

.history-box h3 {
  font-size: 1rem;
  margin-bottom: 10px;
  color: #2c3e50;
}

.history-box ul {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.history-box li {
  background: #2c3e50;
  color: #fff;
  padding: 6px 14px;
  border-radius: 20px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: background 0.2s;
}

.history-box li:hover {
  background: #1a252f;
}
</style>