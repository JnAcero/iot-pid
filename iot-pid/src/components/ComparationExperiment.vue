<template>
  <div>
    <h2>Comparación de Experimentos</h2>
    <div>
      <h3>Experimento 1</h3>
      <canvas id="experiment1"></canvas>
    </div>
    <div>
      <h3>Experimento 2</h3>
      <canvas id="experiment2"></canvas>
    </div>
    <div>
      <h3>Experimento 3</h3>
      <canvas id="experiment3"></canvas>
    </div>
  </div>
</template>

<script>
import { ref, onValue, query, limitToLast } from "firebase/database";
import { database } from "@/firebaseConfig";
import Chart from 'chart.js/auto';
import { auth } from "@/firebaseConfig";

export default {
  mounted() {
    this.loadExperimentData(1);
    this.loadExperimentData(2);
    this.loadExperimentData(3);
  },
  methods: {
    loadExperimentData(experimentId) {
      const uid = auth.currentUser.uid;
      const experimentRef = query(ref(database, `UsersData/${uid}/Muestras_Temperatura/Experimento${experimentId}`), limitToLast(400));

      onValue(experimentRef, (snapshot) => {
        const data = snapshot.val();
        if (data) {
          const labels = [];
          const tempActual = [];
          const tempReferencia = [];
          const sortedKeys = Object.keys(data).sort((a, b) => a - b);

          sortedKeys.forEach(key => {
            const entry = data[key];
            labels.push(parseInt(key));
            tempActual.push(entry.tempActual);
            tempReferencia.push(entry.tempReferencia);
          });

          const ctx = document.getElementById(`experiment${experimentId}`).getContext('2d');
          new Chart(ctx, {
            type: 'line',
            data: {
              labels: labels,
              datasets: [
                {
                  label: 'Temperatura Actual',
                  data: tempActual,
                  borderColor: 'rgba(75, 192, 192, 1)',
                  borderWidth: 1,
                },
                {
                  label: 'Temperatura de Referencia',
                  data: tempReferencia,
                  borderColor: 'rgba(255, 99, 132, 1)',
                  borderWidth: 1,
                },
              ],
            },
            options: {
              scales: {
                x: {
                  type: 'linear',
                  position: 'bottom',
                },
              },
            },
          });
        }
      });
    },
  },
};
</script>

<style scoped>
/* Tu CSS existente */
</style>

  