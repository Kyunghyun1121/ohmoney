<template>
  <div class="p-4 mt-4 monthly-analysis-container">
    <div class="title">
      <h4 class="fw-bold">{{ currentMonth }}월 분석</h4>
      <div class="text-subtitle">
        이번 달에는 지난 달보다
        <span class="fw-bold">{{ Math.abs(gap).toLocaleString() }}원</span>
        {{ gap > 0 ? '더' : '덜' }} 쓰셨어요!
      </div>
    </div>
    <div class="chart-container gap-4 flex-wrap">
      <ThreeMonthsAnalysisChart />
      <IncomeExpenseChart />
    </div>
  </div>
</template>

<script setup>
import IncomeExpenseChart from '@/components/charts/IncomeExpenseChart.vue'
import ThreeMonthsAnalysisChart from '@/components/charts/ThreeMonthsAnalysisChart.vue'
import { useThreeMonthsAnalysis } from '@/stores/analysisStore'
import { useUserStore } from '@/stores/userStore'
import { computed, onMounted } from 'vue'

const userStore = useUserStore()
const userId = userStore.id

const store = useThreeMonthsAnalysis()

const now = new Date()
const currentMonth = now.getMonth() + 1
const currentKey = `${now.getFullYear()}-${String(currentMonth).padStart(2, '0')}`
const lastMonthDate = new Date(now.getFullYear(), currentMonth - 2, 1)
const lastMonthKey = `${lastMonthDate.getFullYear()}-${String(lastMonthDate.getMonth() + 1).padStart(2, '0')}`

onMounted(() => {
  store.fetchThreeMonths(userId, now.getFullYear(), currentMonth)
})

// 이번 달과 지난 달 비교
const currentExpense = computed(() => store.analysis[currentKey]?.expenseTotal || 0)
const lastMonthExpense = computed(() => store.analysis[lastMonthKey]?.expenseTotal || 0)
const gap = computed(() => currentExpense.value - lastMonthExpense.value)
</script>

<style scoped>
.chart-container {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
}

@media (min-width: 641px) {
  .monthly-analysis-container {
    background-color: white;
    border-radius: 20px;
  }
}

@media (max-width: 640px) {
  .monthly-analysis-container {
    max-width: 500px;
  }
}
</style>
