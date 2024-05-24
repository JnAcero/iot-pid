<template>
  <div>
    <canvas ref="lineChartCanvas"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue';
import { Chart, registerables } from 'chart.js/auto';
import 'chartjs-adapter-date-fns'; // Importa el adaptador de fechas

export default {
  name: 'LineChart',
  props: {
    experimentData: {
      type: Object,
      required: true
    },
    experimentId: {
      type: String,
      required: true
    }
  },
  setup(props) {
    const lineChartCanvas = ref(null);
    let chart = null;

    const updateChart = () => {
      if (lineChartCanvas.value) {
        const ctx = lineChartCanvas.value.getContext('2d');

        const dataEntries = Object.entries(props.experimentData);
        const limitedDataEntries = dataEntries.slice(-500); // Solo los últimos 500 datos

        const labels = limitedDataEntries.map(([timestamp]) => new Date(parseInt(timestamp)));
        const actualTemperatures = limitedDataEntries.map(([, data]) => data.Temperatura_Actual);
        const referenceTemperatures = limitedDataEntries.map(([, data]) => data.Temperatura_Referencia);

        if (chart) {
          chart.destroy();
        }

        chart = new Chart(ctx, {
          type: 'line',
          data: {
            labels: labels,
            datasets: [
              {
                label: 'Temperatura Actual',
                data: actualTemperatures,
                borderColor: 'rgba(75, 192, 192, 1)',
                backgroundColor: 'rgba(75, 192, 192, 0.2)',
                fill: false,
              },
              {
                label: 'Temperatura Referencia',
                data: referenceTemperatures,
                borderColor: 'rgba(255, 99, 132, 1)',
                backgroundColor: 'rgba(255, 99, 132, 0.2)',
                fill: false,
              }
            ]
          },
          options: {
            scales: {
              x: {
                type: 'time',
                time: {
                  unit: 'minute'
                },
                title: {
                  display: true,
                  text: 'Tiempo'
                }
              },
              y: {
                title: {
                  display: true,
                  text: 'Temperatura (°C)'
                }
              }
            }
          }
        });
      }
    };

    onMounted(updateChart);

    watch(() => props.experimentData, updateChart);

    return {
      lineChartCanvas
    };
  }
};
</script>

<style scoped>
canvas {
  max-width: 100%;
}
</style>


