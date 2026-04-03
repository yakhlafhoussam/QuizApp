<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const goHome = () => {
  router.push('/')
}
</script>

<template>
  <div class="page">
    <div class="card">
      <h1>Result Page</h1>
      <p>This is the result page. Later we will show the final score here.</p>
      <button @click="goHome">Play Again</button>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #111827;
  color: white;
  padding: 20px;
}

.card {
  background: #1f2937;
  padding: 32px;
  border-radius: 16px;
  width: 100%;
  max-width: 500px;
  text-align: center;
}

h1 {
  margin-bottom: 12px;
  font-size: 32px;
}

p {
  margin-bottom: 24px;
  color: #d1d5db;
}

button {
  background: #16a34a;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 10px;
  cursor: pointer;
}

button:hover {
  background: #15803d;
}
</style>