<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const startQuiz = () => {
  localStorage.removeItem('quiz_app_state')
  router.push('/quiz')
}
</script>

<template>
  <div class="page">
    <div class="card">
      <p class="badge">Vue 3 + TypeScript Quiz</p>
      <h1>Challenge Your Knowledge</h1>
      <p class="description">
        Pass 3 progressive levels, answer 5 questions per level, and finish before the timer ends.
      </p>

      <div class="rules">
        <div class="rule-item">Level 1: minimum 40/100</div>
        <div class="rule-item">Level 2: minimum 60/100</div>
        <div class="rule-item">Level 3: minimum 80/100</div>
        <div class="rule-item">Global timer: 5 minutes</div>
      </div>

      <button @click="startQuiz">Start Quiz</button>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #020617, #0f172a);
  color: white;
  padding: 20px;
}

.card {
  background: rgba(30, 41, 59, 0.95);
  padding: 36px;
  border-radius: 20px;
  width: 100%;
  max-width: 620px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.35);
}

.badge {
  display: inline-block;
  padding: 8px 14px;
  border-radius: 999px;
  background: #1d4ed8;
  font-size: 14px;
  margin-bottom: 18px;
}

h1 {
  margin-bottom: 14px;
  font-size: 40px;
  line-height: 1.2;
}

.description {
  margin-bottom: 24px;
  color: #cbd5e1;
  font-size: 17px;
}

.rules {
  display: grid;
  gap: 12px;
  margin-bottom: 28px;
  text-align: left;
}

.rule-item {
  background: #334155;
  padding: 14px 16px;
  border-radius: 12px;
  color: #e2e8f0;
}

button {
  background: #2563eb;
  color: white;
  border: none;
  padding: 14px 28px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
}

button:hover {
  background: #1d4ed8;
}
</style>