<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import { useRouter } from 'vue-router'
import allQuestions from '../data/questions.json'
import type { Question } from '../types/quiz'

const router = useRouter()

const allQuizQuestions = allQuestions as Question[]

const STORAGE_KEY = 'quiz_app_state'

const currentLevel = ref(1)
const currentQuestionIndex = ref(0)
const totalScore = ref(0)
const levelScore = ref(0)
const selectedAnswer = ref<string | null>(null)

const timeLeft = ref(300)
let timer: number | null = null

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

const selectAnswer = (choice: string) => {
  selectedAnswer.value = choice
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
  selectedAnswer.value = null
}

const nextQuestion = () => {
  if (!selectedAnswer.value || !currentQuestion.value) return

  if (selectedAnswer.value === currentQuestion.value.correct_answer) {
    totalScore.value += 20
    levelScore.value += 20
  }

  if (currentQuestionIndex.value < levelQuestions.value.length - 1) {
    currentQuestionIndex.value++
    selectedAnswer.value = null
  } else {
    finishLevel()
  }
}

const goHome = () => {
  stopTimer()
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
})
</script>

<template>
  <div class="page">
    <div class="quiz-container">
      <div class="top-bar">
        <div>
          <h1>Level {{ currentLevel }}</h1>
          <p>Question {{ currentQuestionIndex + 1 }} / {{ levelQuestions.length }}</p>
          <p class="level-rule">
            Required score for this level: {{ passingScores[currentLevel] }}/100
          </p>
        </div>

        <div class="score-wrapper">
          <div class="score-box">Total Score: {{ totalScore }}</div>
          <div class="score-box">Level Score: {{ levelScore }}</div>
          <div class="time-box">Time Left: {{ formattedTime }}</div>
        </div>
      </div>

      <div class="progress-wrapper">
        <div class="progress-bar" :style="{ width: `${progress}%` }"></div>
      </div>

      <div v-if="currentQuestion" class="question-card">
        <h2>{{ currentQuestion.question }}</h2>

        <div class="choices">
          <button
            v-for="choice in currentQuestion.choices"
            :key="choice"
            @click="selectAnswer(choice)"
            :class="['choice-btn', selectedAnswer === choice ? 'selected' : '']"
          >
            {{ choice }}
          </button>
        </div>

        <div class="actions">
          <button class="secondary" @click="goHome">Back Home</button>
          <button class="primary" @click="nextQuestion" :disabled="!selectedAnswer">
            {{ currentQuestionIndex === levelQuestions.length - 1 ? 'Finish Level' : 'Next' }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  background: linear-gradient(135deg, #020617, #0f172a);
  color: white;
  padding: 24px;
}

.quiz-container {
  max-width: 900px;
  margin: 0 auto;
  padding-top: 40px;
}

.top-bar {
  display: flex;
  justify-content: space-between;
  gap: 16px;
  align-items: flex-start;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.top-bar h1 {
  font-size: 32px;
  margin-bottom: 8px;
}

.top-bar p {
  color: #cbd5e1;
  margin-bottom: 6px;
}

.level-rule {
  color: #93c5fd;
  font-weight: 600;
}

.score-wrapper {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.score-box,
.time-box {
  background: #1e293b;
  padding: 12px 18px;
  border-radius: 12px;
  font-weight: bold;
}

.time-box {
  color: #fca5a5;
}

.progress-wrapper {
  width: 100%;
  height: 12px;
  background: #1e293b;
  border-radius: 999px;
  overflow: hidden;
  margin-bottom: 24px;
}

.progress-bar {
  height: 100%;
  background: #22c55e;
  transition: width 0.3s ease;
}

.question-card {
  background: #1e293b;
  border-radius: 20px;
  padding: 28px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.question-card h2 {
  font-size: 28px;
  margin-bottom: 24px;
  line-height: 1.4;
}

.choices {
  display: grid;
  gap: 14px;
  margin-bottom: 24px;
}

.choice-btn {
  width: 100%;
  text-align: left;
  padding: 16px;
  border: none;
  border-radius: 14px;
  background: #334155;
  color: white;
  cursor: pointer;
  font-size: 16px;
  transition: 0.25s;
}

.choice-btn:hover {
  background: #475569;
}

.choice-btn.selected {
  background: #2563eb;
}

.actions {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  flex-wrap: wrap;
}

.primary,
.secondary {
  border: none;
  padding: 12px 22px;
  border-radius: 12px;
  cursor: pointer;
  font-size: 16px;
}

.primary {
  background: #16a34a;
  color: white;
}

.primary:hover {
  background: #15803d;
}

.primary:disabled {
  background: #64748b;
  cursor: not-allowed;
}

.secondary {
  background: #475569;
  color: white;
}

.secondary:hover {
  background: #334155;
}
</style>