<template>
  <div v-if="!data" class="flex justify-center items-center min-h-[30vh]">
    <h2 class="text-xl font-medium text-gray-500">Insights will appear here</h2>
  </div>

  <div v-else class="flex justify-center px-4 py-6">
    <div
      class="flex flex-col items-center gap-6 rounded-xl shadow-lg border border-gray-200 bg-white p-8 w-full max-w-3xl"
    >
      <h2 class="text-2xl font-bold text-[#010165] mb-4">Valuable Insights</h2>

      <div class="grid grid-cols-1 sm:grid-cols-3 gap-6 w-full text-center">
        <div class="bg-[#f0f4ff] p-4 rounded-lg shadow-inner">
          <p class="text-gray-700 font-medium">Product Saved</p>
          <p class="text-2xl font-bold text-[#010165] mt-1">{{ productSaved }}</p>
        </div>

        <div class="bg-[#e6f9f0] p-4 rounded-lg shadow-inner">
          <p class="text-gray-700 font-medium">Total Savings</p>
          <p class="text-2xl font-bold text-green-600 mt-1">${{ totalSavings }}</p>
        </div>

        <div class="bg-[#fff4e6] p-4 rounded-lg shadow-inner">
          <p class="text-gray-700 font-medium">% of Money Saved</p>
          <p class="text-2xl font-bold text-green-600 mt-1">{{ percentage }}%</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  data: Array,
})

const productSaved = ref(0)
const totalSavings = ref(0)
const percentage = ref(0)

// Compute amount of product saved and total savings
// The properties we're interested on are: quantity, recommended_quantity and unit_price

// Product saved: summatory of quantity - recommended_quantity

function computeInsights(data) {
  let originalSummatory = 0
  let summatoryProduct = 0
  let summatorySavings = 0

  if (data) {
    for (const row of data) {
      originalSummatory += row.quantity * row.unit_price
      const difference = row.quantity - row.recommended_quantity
      summatoryProduct += difference
      summatorySavings += difference * row.unit_price
    }
  }

  productSaved.value = summatoryProduct
  totalSavings.value = summatorySavings
  percentage.value = Number(((summatorySavings * 100) / originalSummatory).toFixed(3))

  console.log('Original sum: ' + originalSummatory)
}

// Watch props.data and recompute when it changes
watch(
  () => props.data,
  (newData) => {
    computeInsights(newData)
  },
  { immediate: true }, // Run immediately on mount
)

console.log(props.data)
</script>

<style scoped></style>
