<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const goResult = () => {
  router.push('/result')
}

const goHome = () => {
  router.push('/')
}
</script>

<template>
  <div class="page">
    <div class="card">
      <h1>Quiz Page</h1>
      <p>This is the quiz page. Later we will load questions here from the API.</p>

      <div class="actions">
        <button @click="goResult">Go to Result</button>
        <button @click="goHome" class="secondary">Back Home</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #020617;
  color: white;
  padding: 20px;
}

.card {
  background: #1e293b;
  padding: 32px;
  border-radius: 16px;
  width: 100%;
  max-width: 600px;
  text-align: center;
}

h1 {
  margin-bottom: 12px;
  font-size: 32px;
}

p {
  margin-bottom: 24px;
  color: #cbd5e1;
}

.actions {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
}

button {
  background: #2563eb;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 10px;
  cursor: pointer;
}

button:hover {
  background: #1d4ed8;
}

.secondary {
  background: #475569;
}

.secondary:hover {
  background: #334155;
}
</style>