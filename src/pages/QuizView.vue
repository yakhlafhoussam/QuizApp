<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import { useRouter } from 'vue-router'
import allQuestions from '../data/questions.json'
import type { Question } from '../types/quiz'
import correctSoundFile from '../assets/sounds/correct.mp3'
import wrongSoundFile from '../assets/sounds/wrong.mp3'

const router = useRouter()

const allQuizQuestions = allQuestions as Question[]

const STORAGE_KEY = 'quiz_app_state'

const currentLevel = ref(1)
const currentQuestionIndex = ref(0)
const totalScore = ref(0)
const levelScore = ref(0)
const selectedAnswer = ref<string | null>(null)

const answered = ref(false)
const isCorrect = ref<boolean | null>(null)

const timeLeft = ref(300)
let timer: number | null = null
let nextQuestionTimeout: number | null = null

const correctAudio = new Audio(correctSoundFile)
const wrongAudio = new Audio(wrongSoundFile)

const passingScores: Record<number, number> = {
  1: 40,
  2: 60,
  3: 80,
}

const levelQuestions = computed(() =>
  allQuizQuestions.filter((question) => question.level === currentLevel.value)
)

const currentQuestion = computed(() => levelQuestions.value[currentQuestionIndex.value])

const progress = computed(() => {
  if (levelQuestions.value.length === 0) return 0
  return ((currentQuestionIndex.value + 1) / levelQuestions.value.length) * 100
})

const formattedTime = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60)
  const seconds = timeLeft.value % 60
  return `${minutes}:${seconds.toString().padStart(2, '0')}`
})

const saveState = () => {
  const quizState = {
    currentLevel: currentLevel.value,
    currentQuestionIndex: currentQuestionIndex.value,
    totalScore: totalScore.value,
    levelScore: levelScore.value,
    timeLeft: timeLeft.value,
  }

  localStorage.setItem(STORAGE_KEY, JSON.stringify(quizState))
}

const loadState = () => {
  const savedState = localStorage.getItem(STORAGE_KEY)

  if (!savedState) return

  const parsedState = JSON.parse(savedState)

  currentLevel.value = parsedState.currentLevel ?? 1
  currentQuestionIndex.value = parsedState.currentQuestionIndex ?? 0
  totalScore.value = parsedState.totalScore ?? 0
  levelScore.value = parsedState.levelScore ?? 0
  timeLeft.value = parsedState.timeLeft ?? 300
}

const clearState = () => {
  localStorage.removeItem(STORAGE_KEY)
}

const playSound = (type: 'correct' | 'wrong') => {
  const audio = type === 'correct' ? correctAudio : wrongAudio
  audio.currentTime = 0
  audio.play().catch(() => {})
}

const startTimer = () => {
  timer = window.setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--
    } else {
      stopTimer()
      clearState()
      router.push({
        path: '/result',
        query: {
          status: 'timeout',
          totalScore: totalScore.value.toString(),
        },
      })
    }
  }, 1000)
}

const stopTimer = () => {
  if (timer !== null) {
    clearInterval(timer)
    timer = null
  }
}

const resetAnswerState = () => {
  selectedAnswer.value = null
  answered.value = false
  isCorrect.value = null
}

const finishLevel = () => {
  const requiredScore = passingScores[currentLevel.value]

  if (levelScore.value < requiredScore) {
    stopTimer()
    clearState()
    router.push({
      path: '/result',
      query: {
        status: 'lose',
        totalScore: totalScore.value.toString(),
        level: currentLevel.value.toString(),
        levelScore: levelScore.value.toString(),
        requiredScore: requiredScore.toString(),
      },
    })
    return
  }

  if (currentLevel.value === 3) {
    stopTimer()
    clearState()
    router.push({
      path: '/result',
      query: {
        status: 'win',
        totalScore: totalScore.value.toString(),
      },
    })
    return
  }

  currentLevel.value++
  currentQuestionIndex.value = 0
  levelScore.value = 0
  resetAnswerState()
}

const goToNextStep = () => {
  if (currentQuestionIndex.value < levelQuestions.value.length - 1) {
    currentQuestionIndex.value++
    resetAnswerState()
  } else {
    finishLevel()
  }
}

const selectAnswer = (choice: string) => {
  if (answered.value || !currentQuestion.value) return

  selectedAnswer.value = choice
  answered.value = true

  const correct = choice === currentQuestion.value.correct_answer
  isCorrect.value = correct

  if (correct) {
    totalScore.value += 20
    levelScore.value += 20
    playSound('correct')
  } else {
    playSound('wrong')
  }

  nextQuestionTimeout = window.setTimeout(() => {
    goToNextStep()
  }, 1000)
}

const getChoiceClass = (choice: string) => {
  if (!answered.value || !currentQuestion.value) {
    return selectedAnswer.value === choice ? 'selected' : ''
  }

  if (choice === currentQuestion.value.correct_answer) {
    return 'correct'
  }

  if (choice === selectedAnswer.value && choice !== currentQuestion.value.correct_answer) {
    return 'wrong'
  }

  return 'disabled'
}

const goHome = () => {
  stopTimer()

  if (nextQuestionTimeout !== null) {
    clearTimeout(nextQuestionTimeout)
    nextQuestionTimeout = null
  }

  clearState()
  router.push('/')
}

watch(
  [currentLevel, currentQuestionIndex, totalScore, levelScore, timeLeft],
  () => {
    saveState()
  },
  { deep: true }
)

onMounted(() => {
  loadState()
  startTimer()
})

onBeforeUnmount(() => {
  stopTimer()

  if (nextQuestionTimeout !== null) {
    clearTimeout(nextQuestionTimeout)
    nextQuestionTimeout = null
  }
})
</script>

<template>
  <div class="page">
    <!-- Mario Cloud Decorations -->
    <div class="cloud cloud-1"></div>
    <div class="cloud cloud-2"></div>
    <div class="cloud cloud-3"></div>
    
    <div class="quiz-container">
      <div class="top-bar">
        <div>
          <div class="level-badge">
            <span class="level-icon">⭐</span>
            <h1>WORLD {{ currentLevel }}-{{ currentQuestionIndex + 1 }}</h1>
          </div>
          <p class="question-counter">
            Question {{ currentQuestionIndex + 1 }} / {{ levelQuestions.length }}
          </p>
          <p class="level-rule">
            🌟 Required: {{ passingScores[currentLevel] }}/100 🌟
          </p>
        </div>

        <div class="score-wrapper">
          <div class="score-box coin-box">
            <span class="coin-icon">🪙</span> {{ totalScore }}
          </div>
          <div class="score-box star-box">
            <span class="star-icon">⭐</span> {{ levelScore }}
          </div>
          <div class="time-box">
            <span class="clock-icon">⏰</span> {{ formattedTime }}
          </div>
        </div>
      </div>

      <div class="progress-wrapper">
        <div class="progress-fill" :style="{ width: `${progress}%` }">
          <div class="progress-star"></div>
        </div>
      </div>

      <div v-if="currentQuestion" class="question-card">
        <div class="question-block-icon">?</div>
        <h2>{{ currentQuestion.question }}</h2>

        <div class="choices">
          <button
            v-for="choice in currentQuestion.choices"
            :key="choice"
            @click="selectAnswer(choice)"
            :disabled="answered"
            :class="['choice-btn', getChoiceClass(choice)]"
          >
            <span class="choice-mushroom" v-if="!answered && !getChoiceClass(choice)">🍄</span>
            <span class="choice-mushroom" v-else-if="getChoiceClass(choice) === 'correct'">⭐</span>
            <span class="choice-mushroom" v-else-if="getChoiceClass(choice) === 'wrong'">💀</span>
            <span class="choice-mushroom" v-else>❓</span>
            {{ choice }}
          </button>
        </div>

        <div v-if="answered" class="feedback">
          <div v-if="isCorrect" class="success-card">
            <span class="success-icon">🎉</span>
            <p class="success-text">CORRECT! +20 coins 🪙</p>
          </div>
          <div v-else class="error-card">
            <span class="error-icon">😭</span>
            <p class="error-text">
              WRONG! The answer is: {{ currentQuestion.correct_answer }}
            </p>
          </div>
        </div>

        <div class="actions">
          <button class="home-btn" @click="goHome">
            <span>🏠</span> EXIT WORLD
          </button>
        </div>
      </div>
    </div>

    <!-- Bush decorations -->
    <div class="bush bush-1"></div>
    <div class="bush bush-2"></div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  background: linear-gradient(180deg, #6EC3E8 0%, #4A9FC9 50%, #3B8BB3 100%);
  color: white;
  padding: 24px;
  position: relative;
  overflow-x: hidden;
}

/* Clouds */
.cloud {
  position: absolute;
  background: white;
  border-radius: 100px;
  opacity: 0.9;
  z-index: 0;
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
  top: 30px;
  left: 5%;
}

.cloud-2 {
  width: 150px;
  height: 70px;
  top: 150px;
  right: 5%;
}

.cloud-3 {
  width: 100px;
  height: 50px;
  bottom: 200px;
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
  z-index: 0;
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

.quiz-container {
  max-width: 900px;
  margin: 0 auto;
  padding-top: 40px;
  position: relative;
  z-index: 1;
}

.top-bar {
  display: flex;
  justify-content: space-between;
  gap: 16px;
  align-items: flex-start;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.level-badge {
  display: flex;
  align-items: center;
  gap: 10px;
  background: rgba(0, 0, 0, 0.6);
  padding: 10px 20px;
  border-radius: 20px;
  border-left: 4px solid #FFD700;
}

.level-icon {
  font-size: 28px;
}

.top-bar h1 {
  font-size: 28px;
  margin-bottom: 8px;
  text-shadow: 2px 2px 0 #E52525;
  color: #FFD700;
}

.question-counter {
  color: #FFF;
  font-weight: bold;
  margin-top: 8px;
  text-shadow: 1px 1px 0 #000;
}

.level-rule {
  color: #FFD700;
  font-weight: bold;
  background: rgba(0, 0, 0, 0.5);
  display: inline-block;
  padding: 5px 12px;
  border-radius: 15px;
  margin-top: 5px;
}

.score-wrapper {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.score-box,
.time-box {
  background: rgba(0, 0, 0, 0.7);
  padding: 12px 18px;
  border-radius: 12px;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 8px;
  border: 2px solid #FFD700;
}

.coin-box {
  color: #FFD700;
}

.star-box {
  color: #FFD700;
}

.time-box {
  color: #FF6B6B;
}

.coin-icon, .star-icon, .clock-icon {
  font-size: 20px;
}

.progress-wrapper {
  width: 100%;
  height: 20px;
  background: #5A3E1B;
  border-radius: 10px;
  overflow: hidden;
  margin-bottom: 24px;
  border: 2px solid #FFD700;
  position: relative;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #FFD700 0%, #FFA500 100%);
  transition: width 0.3s ease;
  position: relative;
}

.progress-star {
  position: absolute;
  right: 0;
  top: -10px;
  width: 30px;
  height: 30px;
  background: #FFD700;
  clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%);
}

.question-card {
  background: rgba(0, 0, 0, 0.8);
  border-radius: 20px;
  padding: 28px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  border: 3px solid #FFD700;
  position: relative;
}

.question-block-icon {
  position: absolute;
  top: -25px;
  left: 20px;
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
}

.question-card h2 {
  font-size: 28px;
  margin-bottom: 24px;
  line-height: 1.4;
  color: #FFD700;
  margin-top: 10px;
}

.choices {
  display: grid;
  gap: 14px;
  margin-bottom: 20px;
}

.choice-btn {
  width: 100%;
  text-align: left;
  padding: 16px;
  border: none;
  border-radius: 14px;
  background: linear-gradient(135deg, #E52525 0%, #B01717 100%);
  color: white;
  cursor: pointer;
  font-size: 16px;
  transition: 0.25s;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 10px;
  border: 2px solid #FFD700;
}

.choice-mushroom {
  font-size: 20px;
}

.choice-btn:hover:not(:disabled) {
  transform: translateX(10px);
  background: linear-gradient(135deg, #FF4444 0%, #CC0000 100%);
  box-shadow: 0 0 10px rgba(255, 215, 0, 0.5);
}

.choice-btn.selected {
  background: linear-gradient(135deg, #2563eb 0%, #1e40af 100%);
}

.choice-btn.correct {
  background: linear-gradient(135deg, #16a34a 0%, #0f7a3a 100%);
  border-color: #FFD700;
}

.choice-btn.wrong {
  background: linear-gradient(135deg, #dc2626 0%, #991b1b 100%);
  text-decoration: line-through;
}

.choice-btn.disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.feedback {
  margin-bottom: 20px;
}

.success-card, .error-card {
  padding: 15px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  gap: 15px;
  animation: slideIn 0.5s ease;
}

.success-card {
  background: rgba(22, 163, 74, 0.3);
  border: 2px solid #4ade80;
}

.error-card {
  background: rgba(220, 38, 38, 0.3);
  border: 2px solid #f87171;
}

.success-icon, .error-icon {
  font-size: 32px;
}

.success-text {
  color: #4ade80;
  font-weight: bold;
  margin: 0;
}

.error-text {
  color: #f87171;
  font-weight: bold;
  margin: 0;
}

.actions {
  display: flex;
  justify-content: flex-start;
  gap: 12px;
  flex-wrap: wrap;
}

.home-btn {
  border: none;
  padding: 12px 22px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 16px;
  background: linear-gradient(135deg, #475569 0%, #334155 100%);
  color: white;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: 0.25s;
}

.home-btn:hover {
  background: linear-gradient(135deg, #5a6a7e 0%, #3a4a5e 100%);
  transform: translateY(-2px);
}

@keyframes slideIn {
  from {
    transform: translateX(-20px);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}
</style>