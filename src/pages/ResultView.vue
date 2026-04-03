<script setup lang="ts">
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
const route = useRoute()

const status = computed(() => String(route.query.status || 'lose'))
const totalScore = computed(() => Number(route.query.totalScore || 0))
const level = computed(() => Number(route.query.level || 1))
const levelScore = computed(() => Number(route.query.levelScore || 0))
const requiredScore = computed(() => Number(route.query.requiredScore || 0))

const goHome = () => {
  router.push('/')
}
</script>

<template>
  <div class="page">
    <div class="card">
      <h1 v-if="status === 'win'" class="win-title">YOU WIN !!</h1>
      <h1 v-else class="lose-title">GAME OVER</h1>

      <p class="score-text">Total Score: <strong>{{ totalScore }}</strong></p>

      <template v-if="status === 'lose'">
        <p class="details-text">
          You failed at level <strong>{{ level }}</strong>.
        </p>
        <p class="details-text">
          Level score: <strong>{{ levelScore }}</strong> / Required:
          <strong>{{ requiredScore }}</strong>
        </p>
      </template>

      <template v-else>
        <p class="details-text">
          Congratulations! You passed all 3 levels successfully.
        </p>
      </template>

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
  max-width: 540px;
  text-align: center;
}

.win-title {
  margin-bottom: 18px;
  font-size: 38px;
  color: #22c55e;
}

.lose-title {
  margin-bottom: 18px;
  font-size: 38px;
  color: #ef4444;
}

.score-text {
  margin-bottom: 14px;
  color: #e5e7eb;
  font-size: 20px;
}

.details-text {
  margin-bottom: 10px;
  color: #d1d5db;
  font-size: 17px;
}

button {
  margin-top: 20px;
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