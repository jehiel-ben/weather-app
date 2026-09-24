<template>
  <div class="loading-container" v-show="visible">
    <div class="spinner"></div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  name: 'LoadingMessage',
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    message: {
      type: String,
      default: 'Chargement des données météo...'
    }
  }
}
</script>

<style scoped>
.loading-container {
  text-align: center;
  padding: 40px 20px;
  background: rgba(255, 255, 255, 0.85);
  border-radius: 16px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
}

.spinner {
  width: 50px;
  height: 50px;
  border: 5px solid #e0e0e0;
  border-top: 5px solid #2c3e50;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 15px;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

p {
  font-size: 1rem;
  color: #555;
}
</style>