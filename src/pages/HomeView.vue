<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const startQuiz = () => {
  router.push('/quiz')
}
</script>

<template>
  <div class="page">
    <div class="card">
      <h1>Quiz App</h1>
      <p>Welcome to the quiz game built with Vue 3 and TypeScript.</p>
      <button @click="startQuiz">Start</button>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #0f172a;
  color: white;
  padding: 20px;
}

.card {
  background: #1e293b;
  padding: 32px;
  border-radius: 16px;
  width: 100%;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

h1 {
  margin-bottom: 12px;
  font-size: 32px;
}

p {
  margin-bottom: 24px;
  color: #cbd5e1;
}

button {
  background: #2563eb;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 10px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background: #1d4ed8;
}
</style>