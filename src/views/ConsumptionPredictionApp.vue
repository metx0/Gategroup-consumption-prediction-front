<template>
  <MyNavbar />
  <PredictionSection @data-ready="handleDataReady" />
  <PredictionInsights :data="data" />
  <!-- <ConsumptionForm /> -->
</template>

<script setup>
import MyNavbar from '../components/MyNavbar.vue'
import PredictionSection from './PredictionSection.vue'
import PredictionInsights from './PredictionInsights.vue'
import { ref } from 'vue'
// import ConsumptionForm from './ConsumptionForm.vue'

const data = ref()

const handleDataReady = (newData) => {
  data.value = newData
}

</script>

<style></style>
