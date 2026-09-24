<template>
  <div class="search-bar">
    <input
      type="text"
      v-model="cityInput"
      @keyup.enter="onSearch"
      placeholder="Entrez une ville (ex : Bujumbura, Tokyo...)"
      class="search-input"
    />
    <button @click="onSearch" class="search-btn" :disabled="!cityInput.trim()">
      🔍 Rechercher
    </button>
  </div>
</template>

<script>
export default {
  name: 'SearchBar',
  data() {
    return {
      cityInput: ''
    }
  },
  methods: {
    onSearch() {
      const city = this.cityInput.trim()
      if (!city) return
      this.$emit('search', city)
    }
  }
}
</script>

<style scoped>
.search-bar {
  display: flex;
  gap: 10px;
  margin-bottom: 25px;
}

.search-input {
  flex: 1;
  padding: 14px 18px;
  border: none;
  border-radius: 12px;
  font-size: 1rem;
  outline: none;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.2s;
}

.search-input:focus {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
}

.search-btn {
  padding: 14px 22px;
  border: none;
  border-radius: 12px;
  background: #2c3e50;
  color: white;
  font-size: 1rem;
  cursor: pointer;
  transition: transform 0.15s, background 0.2s;
}

.search-btn:hover:not(:disabled) {
  background: #1a252f;
  transform: translateY(-2px);
}

.search-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>