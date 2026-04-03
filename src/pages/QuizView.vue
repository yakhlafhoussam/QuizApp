<script setup lang="ts">
import { computed, ref } from 'vue'
import { useRouter } from 'vue-router'
import allQuestions from '../data/questions.json'
import type { Question } from '../types/quiz'

const router = useRouter()

const questions = allQuestions as Question[]

const currentQuestionIndex = ref(0)
const score = ref(0)
const selectedAnswer = ref<string | null>(null)

const currentQuestion = computed(() => questions[currentQuestionIndex.value])

const progress = computed(() => {
  return ((currentQuestionIndex.value + 1) / questions.length) * 100
})

const selectAnswer = (choice: string) => {
  selectedAnswer.value = choice
}

const nextQuestion = () => {
  if (!selectedAnswer.value) return

  if (selectedAnswer.value === currentQuestion.value.correct_answer) {
    score.value += 20
  }

  if (currentQuestionIndex.value < questions.length - 1) {
    currentQuestionIndex.value++
    selectedAnswer.value = null
  } else {
    router.push({
      path: '/result',
      query: {
        score: score.value.toString(),
      },
    })
  }
}

const goHome = () => {
  router.push('/')
}
</script>

<template>
  <div class="page">
    <div class="quiz-container">
      <div class="top-bar">
        <div>
          <h1>Quiz</h1>
          <p>Question {{ currentQuestionIndex + 1 }} / {{ questions.length }}</p>
        </div>

        <div class="score-box">
          Score: {{ score }}
        </div>
      </div>

      <div class="progress-wrapper">
        <div class="progress-bar" :style="{ width: `${progress}%` }"></div>
      </div>

      <div class="question-card">
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
            {{ currentQuestionIndex === questions.length - 1 ? 'Finish' : 'Next' }}
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
  max-width: 800px;
  margin: 0 auto;
  padding-top: 40px;
}

.top-bar {
  display: flex;
  justify-content: space-between;
  gap: 16px;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.top-bar h1 {
  font-size: 32px;
  margin-bottom: 8px;
}

.top-bar p {
  color: #cbd5e1;
}

.score-box {
  background: #1e293b;
  padding: 12px 18px;
  border-radius: 12px;
  font-weight: bold;
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