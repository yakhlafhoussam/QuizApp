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
    <!-- Cloud decorations -->
    <div class="cloud cloud-1"></div>
    <div class="cloud cloud-2"></div>
    <div class="cloud cloud-3"></div>
    
    <!-- Bush decorations -->
    <div class="bush bush-1"></div>
    <div class="bush bush-2"></div>

    <div class="card">
      <!-- Game icon -->
      <img src="../assets/images/game.png" alt="Game Icon" class="game-icon" />

      <p class="badge">⭐ MARIO'S CHALLENGE ⭐</p>
      <h1>Power-Up Your<br>Knowledge!</h1>
      <p class="description">
        Pass 3 progressive levels, answer 5 questions per level, and finish before the timer ends.
      </p>

      <div class="rules">
        <div class="rule-item">
          <span class="mushroom">🍄</span>
          Level 1: minimum 40/100
        </div>
        <div class="rule-item">
          <span class="flower">🌻</span>
          Level 2: minimum 60/100
        </div>
        <div class="rule-item">
          <span class="star">⭐</span>
          Level 3: minimum 80/100
        </div>
        <div class="rule-item timer-rule">
          <span class="clock">⏰</span>
          Global timer: 5 minutes
        </div>
      </div>

      <button @click="startQuiz">
        <span class="button-text">START QUEST</span>
        <span class="button-icon">🎮</span>
      </button>
    </div>

    <!-- Question blocks -->
    <div class="question-block block-1">?</div>
    <div class="question-block block-2">?</div>
    <div class="question-block block-3">?</div>
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
  cursor: pointer;
  transition: transform 0.2s;
}

.question-block:hover {
  transform: translateY(-5px);
}

.block-1 {
  bottom: 80px;
  left: 15%;
}

.block-2 {
  bottom: 150px;
  right: 15%;
}

.block-3 {
  bottom: 80px;
  right: 25%;
}

.card {
  background: rgba(0, 0, 0, 0.75);
  padding: 36px;
  border-radius: 20px;
  width: 100%;
  max-width: 620px;
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

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

.badge {
  display: inline-block;
  padding: 8px 14px;
  border-radius: 999px;
  background: #E52525;
  font-size: 14px;
  margin-bottom: 18px;
  font-weight: bold;
  color: #FFD700;
  letter-spacing: 2px;
}

h1 {
  margin-bottom: 14px;
  font-size: 42px;
  line-height: 1.2;
  text-shadow: 3px 3px 0 #E52525;
  color: #FFD700;
}

.description {
  margin-bottom: 24px;
  color: #DDD;
  font-size: 17px;
}

.rules {
  display: grid;
  gap: 12px;
  margin-bottom: 28px;
  text-align: left;
}

.rule-item {
  background: rgba(0, 0, 0, 0.6);
  padding: 14px 16px;
  border-radius: 12px;
  color: #FFD700;
  font-weight: bold;
  border-left: 4px solid #E52525;
  display: flex;
  align-items: center;
  gap: 10px;
}

.mushroom, .flower, .star, .clock {
  font-size: 24px;
}

.timer-rule {
  border-left-color: #FFD700;
}

button {
  background: linear-gradient(180deg, #E52525 0%, #B01717 100%);
  color: #FFD700;
  border: none;
  padding: 14px 28px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 18px;
  font-weight: bold;
  width: 100%;
  position: relative;
  transition: transform 0.2s;
  box-shadow: 0 5px 0 #8B0000;
}

button:hover {
  transform: translateY(-2px);
  box-shadow: 0 7px 0 #8B0000;
}

button:active {
  transform: translateY(5px);
  box-shadow: 0 2px 0 #8B0000;
}

.button-text {
  margin-right: 10px;
}

.button-icon {
  font-size: 20px;
}
</style>