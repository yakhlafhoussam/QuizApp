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
    <!-- Cloud decorations -->
    <div class="cloud cloud-1"></div>
    <div class="cloud cloud-2"></div>
    <div class="cloud cloud-3"></div>
    
    <!-- Bush decorations -->
    <div class="bush bush-1"></div>
    <div class="bush bush-2"></div>
    
    <!-- Flag pole for win screen -->
    <div v-if="status === 'win'" class="flag-pole">
      <div class="flag"></div>
      <div class="pole"></div>
    </div>

    <div class="card">
      <!-- Game icon -->
      <img src="../assets/images/game.png" alt="Game Icon" class="game-icon" />

      <div v-if="status === 'win'" class="win-title">
        <span class="star-icon">⭐</span>
        COURSE CLEAR!
        <span class="star-icon">⭐</span>
      </div>
      <div v-else-if="status === 'timeout'" class="timeout-title">
        <span class="skull-icon">💀</span>
        TIME'S UP!
        <span class="skull-icon">💀</span>
      </div>
      <div v-else class="lose-title">
        <span class="skull-icon">😵</span>
        GAME OVER
        <span class="skull-icon">😵</span>
      </div>

      <div class="score-card">
        <div class="score-row">
          <span class="coin-icon">🪙</span>
          <span class="score-label">Total Coins:</span>
          <span class="score-value">{{ totalScore }}</span>
        </div>
      </div>

      <template v-if="status === 'lose'">
        <div class="details-card">
          <div class="details-row">
            <span class="details-icon">🌍</span>
            <span>Failed at World {{ level }}</span>
          </div>
          <div class="details-row">
            <span class="details-icon">⭐</span>
            <span>Stars collected: {{ levelScore }} / {{ requiredScore }}</span>
          </div>
          <div class="details-row warning">
            <span class="details-icon">😭</span>
            <span>Not enough power to continue...</span>
          </div>
        </div>
      </template>

      <template v-else-if="status === 'timeout'">
        <div class="details-card">
          <div class="details-row">
            <span class="details-icon">⏰</span>
            <span>The clock ran out!</span>
          </div>
          <div class="details-row">
            <span class="details-icon">🏃</span>
            <span>Princess is in another castle...</span>
          </div>
          <div class="details-row warning">
            <span class="details-icon">⌛</span>
            <span>Time expired before finishing all worlds</span>
          </div>
        </div>
      </template>

      <template v-else>
        <div class="details-card win-details">
          <div class="details-row">
            <span class="details-icon">🎉</span>
            <span>You saved the Princess!</span>
          </div>
          <div class="details-row">
            <span class="details-icon">🏆</span>
            <span>All 3 worlds completed!</span>
          </div>
          <div class="details-row">
            <span class="details-icon">⭐</span>
            <span>Mario approves your knowledge!</span>
          </div>
        </div>
      </template>

      <button @click="goHome">
        <span class="button-icon">🎮</span>
        PLAY AGAIN
        <span class="button-icon">🍄</span>
      </button>
    </div>

    <!-- Question block decorations -->
    <div class="question-block block-1">?</div>
    <div class="question-block block-2">?</div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #6EC3E8 0%, #4A9FC9 50%, #3B8BB3 100%);
  color: white;
  padding: 20px;
  position: relative;
  overflow: hidden;
}

/* Clouds */
.cloud {
  position: absolute;
  background: white;
  border-radius: 100px;
  opacity: 0.9;
}

.cloud::before,
.cloud::after {
  content: '';
  position: absolute;
  background: white;
  border-radius: 50%;
}

.cloud::before {
  width: 60px;
  height: 60px;
  top: -30px;
  left: 20px;
}

.cloud::after {
  width: 80px;
  height: 80px;
  top: -40px;
  left: 60px;
}

.cloud-1 {
  width: 120px;
  height: 60px;
  top: 50px;
  left: 5%;
}

.cloud-2 {
  width: 150px;
  height: 70px;
  top: 120px;
  right: 5%;
}

.cloud-3 {
  width: 100px;
  height: 50px;
  bottom: 100px;
  left: 10%;
}

/* Bushes */
.bush {
  position: absolute;
  bottom: 0;
  width: 120px;
  height: 60px;
  background: #2D8B3E;
  border-radius: 80px 80px 40px 40px;
  box-shadow: 0 5px 0 #1B5E2A;
}

.bush::before {
  content: '';
  position: absolute;
  width: 60px;
  height: 50px;
  background: #2D8B3E;
  border-radius: 50%;
  left: -20px;
  bottom: 0;
  box-shadow: 0 5px 0 #1B5E2A;
}

.bush::after {
  content: '';
  position: absolute;
  width: 60px;
  height: 50px;
  background: #2D8B3E;
  border-radius: 50%;
  right: -20px;
  bottom: 0;
  box-shadow: 0 5px 0 #1B5E2A;
}

.bush-1 {
  left: 0;
}

.bush-2 {
  right: 0;
}

/* Flag pole for win screen */
.flag-pole {
  position: absolute;
  top: 20%;
  right: 10%;
  animation: wave 1s ease-in-out infinite;
}

.pole {
  width: 8px;
  height: 150px;
  background: #8B8682;
  position: absolute;
  top: 0;
  left: 50px;
}

.flag {
  width: 80px;
  height: 60px;
  background: #E52525;
  position: absolute;
  top: 0;
  left: 54px;
}

.flag::after {
  content: '★';
  position: absolute;
  top: 15px;
  left: 30px;
  font-size: 30px;
  color: #FFD700;
}

/* Question blocks */
.question-block {
  position: absolute;
  width: 50px;
  height: 50px;
  background: #D4A017;
  border: 3px solid #B8860B;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  font-weight: bold;
  color: #B8860B;
  box-shadow: 0 5px 0 #8B6914;
  animation: bounce 2s infinite;
}

.block-1 {
  bottom: 80px;
  left: 15%;
  animation-delay: 0s;
}

.block-2 {
  bottom: 120px;
  right: 15%;
  animation-delay: 0.5s;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

@keyframes wave {
  0%, 100% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(5deg);
  }
}

.card {
  background: rgba(0, 0, 0, 0.85);
  padding: 40px;
  border-radius: 20px;
  width: 100%;
  max-width: 600px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  border: 3px solid #FFD700;
  position: relative;
  z-index: 2;
}

/* Game Icon */
.game-icon {
  width: 80px;
  height: 80px;
  margin: 0 auto 20px;
  display: block;
  animation: bounce 2s infinite;
}

.win-title, .lose-title, .timeout-title {
  margin-bottom: 25px;
  font-size: 42px;
  font-weight: bold;
  text-shadow: 3px 3px 0 #000;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
}

.win-title {
  color: #FFD700;
}

.lose-title {
  color: #E52525;
}

.timeout-title {
  color: #FFA500;
}

.star-icon, .skull-icon {
  font-size: 40px;
  animation: spin 0.5s ease;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.score-card {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border-radius: 15px;
  padding: 20px;
  margin-bottom: 20px;
  border: 2px solid #FFD700;
}

.score-row {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  font-size: 28px;
  font-weight: bold;
}

.coin-icon {
  font-size: 32px;
}

.score-label {
  color: #FFD700;
}

.score-value {
  color: #FFD700;
  font-size: 32px;
}

.details-card {
  background: rgba(0, 0, 0, 0.5);
  border-radius: 12px;
  padding: 15px;
  margin-bottom: 20px;
}

.details-row {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px;
  font-size: 16px;
  color: #DDD;
  border-bottom: 1px solid rgba(255, 215, 0, 0.3);
}

.details-row:last-child {
  border-bottom: none;
}

.details-icon {
  font-size: 24px;
}

.warning {
  color: #FFA500;
  font-weight: bold;
}

.win-details {
  background: rgba(34, 197, 94, 0.2);
  border: 1px solid #4ade80;
}

button {
  margin-top: 10px;
  background: linear-gradient(180deg, #E52525 0%, #B01717 100%);
  color: #FFD700;
  border: none;
  padding: 14px 28px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 18px;
  font-weight: bold;
  width: 100%;
  transition: transform 0.2s;
  box-shadow: 0 5px 0 #8B0000;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

button:hover {
  transform: translateY(-2px);
  box-shadow: 0 7px 0 #8B0000;
}

button:active {
  transform: translateY(5px);
  box-shadow: 0 2px 0 #8B0000;
}

.button-icon {
  font-size: 20px;
}
</style>