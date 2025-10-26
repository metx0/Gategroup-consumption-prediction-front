<template>
  <div class="min-h-screen bg-gray-50">
    <div class="max-w-7xl mx-auto px-8 py-8">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="lg:col-span-1">
          <div class="bg-white rounded-lg shadow-md border border-gray-200 p-6 sticky top-8">
            <h2 class="text-xl font-semibold text-gray-900 mb-6 pb-3 border-b border-gray-200">
              Flight Information
            </h2>

            <form @submit.prevent="handleSubmit" class="space-y-5">
              <div class="flex flex-col">
                <label class="text-gray-700 font-medium mb-2 text-sm">Airline</label>
                <input
                  v-model="formData.airline"
                  type="text"
                  placeholder="e.g., Delta Airlines"
                  class="w-full px-4 py-2.5 bg-white border border-gray-300 rounded-lg text-gray-900 placeholder-gray-400 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 transition-all"
                  required
                />
              </div>

              <div class="flex flex-col">
                <label class="text-gray-700 font-medium mb-2 text-sm">Product</label>
                <input
                  v-model="formData.product"
                  type="text"
                  placeholder="e.g., Premium Meals"
                  class="w-full px-4 py-2.5 bg-white border border-gray-300 rounded-lg text-gray-900 placeholder-gray-400 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 transition-all"
                  required
                />
              </div>

              <div class="flex flex-col">
                <label class="text-gray-700 font-medium mb-2 text-sm">Departure Time</label>
                <input
                  v-model="formData.departureTime"
                  type="date"
                  class="w-full px-4 py-2.5 bg-white border border-gray-300 rounded-lg text-gray-900 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 transition-all"
                  required
                />
              </div>

              <div class="flex flex-col">
                <label class="text-gray-700 font-medium mb-2 text-sm">Arrival Time</label>
                <input
                  v-model="formData.arrivalTime"
                  type="datetime-local"
                  class="w-full px-4 py-2.5 bg-white border border-gray-300 rounded-lg text-gray-900 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 transition-all"
                  required
                />
              </div>

              <!-- Quantity Required -->
              <div class="flex flex-col">
                <label class="text-gray-700 font-medium mb-2 text-sm">Quantity Required</label>
                <input
                  v-model.number="formData.quantityRequired"
                  type="number"
                  min="1"
                  placeholder="Enter quantity"
                  class="w-full px-4 py-2.5 bg-white border border-gray-300 rounded-lg text-gray-900 placeholder-gray-400 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/20 transition-all"
                  required
                />
              </div>

              <!-- Submit Button -->
              <button
                type="submit"
                class="w-full px-6 py-3 bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 text-white font-semibold rounded-lg shadow-md hover:shadow-lg transition-all duration-200"
              >
                Generate Results
              </button>
            </form>
          </div>
        </div>

        <!-- Results - Right Column -->
        <div class="lg:col-span-2 space-y-6">
          <!-- Summary Cards -->
          <div v-if="showResults" class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <!-- Products Not Wasted -->
            <div class="bg-white rounded-lg shadow-md border border-gray-200 p-6">
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <p class="text-gray-600 text-sm font-medium mb-2">Products Not Wasted</p>
                  <p class="text-4xl font-bold text-gray-900 mb-1">
                    {{ summary.productsNotWasted }}
                  </p>
                  <div class="flex items-center gap-1 mt-2">
                    <svg
                      class="w-4 h-4 text-green-600"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"
                      ></path>
                    </svg>
                    <span class="text-green-600 text-sm font-medium"
                      >{{ wastePercentage }}% waste reduction</span
                    >
                  </div>
                </div>
                <div class="bg-green-50 p-3 rounded-lg">
                  <svg
                    class="w-8 h-8 text-green-600"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                    ></path>
                  </svg>
                </div>
              </div>
            </div>

            <!-- Total Savings -->
            <div class="bg-white rounded-lg shadow-md border border-gray-200 p-6">
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <p class="text-gray-600 text-sm font-medium mb-2">Total Savings</p>
                  <p class="text-4xl font-bold text-gray-900 mb-1">
                    ${{ summary.totalSavings.toLocaleString() }}
                  </p>
                  <div class="flex items-center gap-1 mt-2">
                    <svg
                      class="w-4 h-4 text-blue-600"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"
                      ></path>
                    </svg>
                    <span class="text-blue-600 text-sm font-medium"
                      >Cost optimization achieved</span
                    >
                  </div>
                </div>
                <div class="bg-blue-50 p-3 rounded-lg">
                  <svg
                    class="w-8 h-8 text-blue-600"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                    ></path>
                  </svg>
                </div>
              </div>
            </div>
          </div>

          <!-- Results Table -->
          <div
            v-if="showResults"
            class="bg-white rounded-lg shadow-md border border-gray-200 overflow-hidden"
          >
            <div class="px-6 py-4 border-b border-gray-200 bg-gray-50">
              <h2 class="text-xl font-semibold text-gray-900">Optimization Results</h2>
            </div>

            <div class="overflow-x-auto">
              <table class="w-full">
                <thead class="bg-gray-50 border-b border-gray-200">
                  <tr>
                    <th
                      class="px-6 py-3 text-left text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Item ID
                    </th>
                    <th
                      class="px-6 py-3 text-left text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Description
                    </th>
                    <th
                      class="px-6 py-3 text-right text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Required Qty
                    </th>
                    <th
                      class="px-6 py-3 text-right text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Recommended Qty
                    </th>
                    <th
                      class="px-6 py-3 text-right text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Price
                    </th>
                    <th
                      class="px-6 py-3 text-right text-xs font-semibold text-gray-700 uppercase tracking-wider"
                    >
                      Savings
                    </th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200 bg-white">
                  <tr
                    v-for="item in results"
                    :key="item.itemId"
                    class="hover:bg-gray-50 transition-colors"
                  >
                    <td class="px-6 py-4 text-sm font-mono text-blue-600 font-medium">
                      {{ item.itemId }}
                    </td>
                    <td class="px-6 py-4 text-sm text-gray-900 font-medium">
                      {{ item.description }}
                    </td>
                    <td class="px-6 py-4 text-sm text-gray-600 text-right">
                      {{ item.requiredQuantity }}
                    </td>
                    <td class="px-6 py-4 text-sm text-right">
                      <span :class="getQuantityColor(item)" class="font-semibold">
                        {{ item.recommendedQuantity }}
                      </span>
                    </td>
                    <td class="px-6 py-4 text-sm text-gray-900 font-medium text-right">
                      ${{ item.price.toFixed(2) }}
                    </td>
                    <td class="px-6 py-4 text-right">
                      <span
                        class="inline-flex items-center px-3 py-1 rounded-full text-sm font-semibold"
                        :class="getSavingsBadgeClass(item)"
                      >
                        ${{ item.savings.toFixed(2) }}
                      </span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- Empty State -->
          <div
            v-if="!showResults"
            class="bg-white rounded-lg shadow-md border border-gray-200 border-dashed p-16 text-center"
          >
            <div
              class="bg-gray-100 w-20 h-20 rounded-full flex items-center justify-center mx-auto mb-4"
            >
              <svg
                class="w-10 h-10 text-gray-400"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="1.5"
                  d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"
                ></path>
              </svg>
            </div>
            <h3 class="text-lg font-semibold text-gray-900 mb-2">No Results Yet</h3>
            <p class="text-gray-600">
              Fill out the form and click "Generate Results" to see optimization data
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

// get results from API
const showResults = ref(false)
const formData = reactive({
  airline: '',
  product: '',
  departureTime: '',
  arrivalTime: '',
  quantityRequired: null,
})

const results = ref([])
const summary = ref({
  productsNotWasted: 0,
  totalSavings: 0,
})

const wastePercentage = computed(() => {
  if (formData.quantityRequired) {
    return ((summary.value.productsNotWasted / formData.quantityRequired) * 100).toFixed(1)
  }
  return 0
})

const handleSubmit = () => {
  generateResults()
  showResults.value = true
}

const getQuantityColor = (item) => {
  const diff = item.requiredQuantity - item.recommendedQuantity
  if (diff > 0) return 'text-green-600'
  if (diff < 0) return 'text-orange-600'
  return 'text-gray-600'
}

const getSavingsBadgeClass = (item) => {
  if (item.savings > 10) {
    return 'bg-green-100 text-green-700 border border-green-200'
  } else if (item.savings > 0) {
    return 'bg-blue-100 text-blue-700 border border-blue-200'
  }
  return 'bg-gray-100 text-gray-700 border border-gray-200'
}
</script>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type='number'] {
  -moz-appearance: textfield;
}

input[type='datetime-local']::-webkit-calendar-picker-indicator {
  cursor: pointer;
}

::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #f3f4f6;
}

::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #9ca3af;
}
</style>
