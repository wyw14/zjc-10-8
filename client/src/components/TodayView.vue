<template>
  <div class="card">
    <span class="date-label">📅 {{ formatDate(data.date) }}</span>

    <div v-if="data.answered" class="status-badge status-answered">
      ✓ 今日已回答
    </div>
    <div v-else class="status-badge status-unanswered">
      ⏰ 等待你的回答
    </div>

    <div class="question-text">
      {{ data.question.question }}
    </div>

    <label class="answer-label">✍️ 我的回答：</label>

    <textarea
      v-model="localAnswer"
      class="answer-textarea"
      placeholder="在这里写下你的思考和答案..."
      :disabled="submitting"
    ></textarea>

    <button
      class="submit-btn"
      :disabled="submitting || !localAnswer.trim()"
      @click="handleSubmit"
    >
      {{ submitting ? '保存中...' : '💾 保存回答' }}
    </button>

    <div v-if="data.answered && data.answer" class="displayed-answer">
      <h4>📝 已保存的回答：</h4>
      <p>{{ data.answer }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  todayData: Object,
  submitting: Boolean
})

const emit = defineEmits(['submit'])

const data = ref(props.todayData)
const localAnswer = ref('')

watch(() => props.todayData, (newVal) => {
  if (newVal) {
    data.value = newVal
    localAnswer.value = newVal.answer || ''
  }
}, { immediate: true })

function formatDate(dateStr) {
  if (!dateStr) return ''
  const [y, m, d] = dateStr.split('-').map(Number)
  const date = new Date(y, m - 1, d)
  const weekdays = ['周日', '周一', '周二', '周三', '周四', '周五', '周六']
  return `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日 ${weekdays[date.getDay()]}`
}

function handleSubmit() {
  emit('submit', localAnswer.value)
}
</script>
