<template>
  <canvas ref="canvas"></canvas>
</template>

<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'
import { Chart, registerables } from 'chart.js'

// Registrar todos os componentes do Chart.js (escala, tooltip, legend, etc)
Chart.register(...registerables)

const props = defineProps({
  chartData: Object,
  chartOptions: Object,
})

const canvas = ref(null)
let chartInstance = null

const renderChart = () => {
  if (chartInstance) {
    chartInstance.destroy()
  }

  chartInstance = new Chart(canvas.value, {
    type: 'bar', // define o tipo aqui!
    data: props.chartData,
    options: props.chartOptions,
  })
}

onMounted(() => {
  renderChart()
})

watch(
  () => props.chartData,
  () => {
    renderChart()
  },
  { deep: true },
)

onBeforeUnmount(() => {
  if (chartInstance) {
    chartInstance.destroy()
  }
})
</script>
