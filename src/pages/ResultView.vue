<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import winSoundFile from '../assets/sounds/win.mp3'
import gameOverSoundFile from '../assets/sounds/gameover.mp3'

const router = useRouter()
const route = useRoute()

const status = computed(() => String(route.query.status || 'lose'))
const totalScore = computed(() => Number(route.query.totalScore || 0))
const level = computed(() => Number(route.query.level || 1))
const levelScore = computed(() => Number(route.query.levelScore || 0))
const requiredScore = computed(() => Number(route.query.requiredScore || 0))

const winAudio = new Audio(winSoundFile)
const gameOverAudio = new Audio(gameOverSoundFile)

const playResultSound = () => {
  if (status.value === 'win') {
    winAudio.currentTime = 0
    winAudio.play().catch(() => {})
  } else {
    gameOverAudio.currentTime = 0
    gameOverAudio.play().catch(() => {})
  }
}

const goHome = () => {
  localStorage.removeItem('quiz_app_state')
  router.push('/')
}

onMounted(() => {
  playResultSound()
})
</script>

<template>
  <div class="page">
    <div class="card">
      <h1 v-if="status === 'win'" class="win-title">YOU WIN !!</h1>
      <h1 v-else-if="status === 'timeout'" class="timeout-title">GAME OVER</h1>
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

      <template v-else-if="status === 'timeout'">
        <p class="details-text">
          The time expired before finishing all levels.
        </p>
      </template>

      <template v-else>
        <p class="details-text">
          Excellent work. You passed all 3 levels successfully.
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
  background: linear-gradient(135deg, #030712, #111827);
  color: white;
  padding: 20px;
}

.card {
  background: #1f2937;
  padding: 36px;
  border-radius: 20px;
  width: 100%;
  max-width: 560px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.35);
}

.win-title {
  margin-bottom: 18px;
  font-size: 40px;
  color: #22c55e;
}

.lose-title {
  margin-bottom: 18px;
  font-size: 40px;
  color: #ef4444;
}

.timeout-title {
  margin-bottom: 18px;
  font-size: 40px;
  color: #f59e0b;
}

.score-text {
  margin-bottom: 14px;
  color: #e5e7eb;
  font-size: 22px;
}

.details-text {
  margin-bottom: 10px;
  color: #d1d5db;
  font-size: 17px;
  line-height: 1.6;
}

button {
  margin-top: 24px;
  background: #16a34a;
  color: white;
  border: none;
  padding: 14px 28px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
}

button:hover {
  background: #15803d;
}
</style>