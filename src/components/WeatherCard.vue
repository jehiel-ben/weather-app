<template>
  <div class="weather-card" v-if="weather">
    <div class="card-header">
      <h2>{{ weather.city }}, {{ weather.country }}</h2>
      <img :src="weather.iconUrl" :alt="weather.condition" class="weather-icon" />
    </div>

    <p class="condition">{{ weather.condition }}</p>

    <div class="temp-main">
      {{ convertedTemp }}<span>{{ unitSymbol }}</span>
    </div>

    <div class="details-grid">
      <div class="detail-item" v-for="detail in detailsList" :key="detail.label">
        <span class="detail-icon">{{ detail.icon }}</span>
        <div>
          <p class="detail-label">{{ detail.label }}</p>
          <p class="detail-value">{{ detail.value }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WeatherCard',
  props: {
    weather: {
      type: Object,
      default: null
    },
    unit: {
      type: String,
      default: 'celsius'
    }
  },
  computed: {
    unitSymbol() {
      return this.unit === 'celsius' ? '°C' : '°F'
    },
    convertedTemp() {
      if (!this.weather) return 0
      return this.unit === 'celsius'
        ? this.weather.temperature
        : Math.round((this.weather.temperature * 9) / 5 + 32)
    },
    detailsList() {
      if (!this.weather) return []
      return [
        { icon: '💧', label: 'Humidité', value: `${this.weather.humidity} %` },
        { icon: '💨', label: 'Vent', value: `${this.weather.windSpeed} km/h` }
      ]
    }
  }
}
</script>

<style scoped>
.weather-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 12px 35px rgba(0, 0, 0, 0.15);
  text-align: center;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-header h2 {
  font-size: 1.6rem;
  color: #2c3e50;
}

.weather-icon {
  width: 80px;
  height: 80px;
}

.condition {
  font-size: 1.1rem;
  color: #555;
  margin: 10px 0 20px;
}

.temp-main {
  font-size: 4rem;
  font-weight: 700;
  color: #2c3e50;
  line-height: 1;
}

.temp-main span {
  font-size: 2rem;
  font-weight: 400;
}

.details-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin-top: 25px;
}

.detail-item {
  display: flex;
  align-items: center;
  gap: 10px;
  background: #f4f7fb;
  padding: 12px;
  border-radius: 12px;
  text-align: left;
}

.detail-icon {
  font-size: 1.5rem;
}

.detail-label {
  font-size: 0.8rem;
  color: #888;
}

.detail-value {
  font-size: 1rem;
  font-weight: 600;
  color: #2c3e50;
}
</style>