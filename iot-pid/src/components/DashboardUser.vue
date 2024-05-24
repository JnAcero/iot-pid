<template>
  <main class="container">
    <h2 class="mb-4">Dashboard</h2>
    <button @click="logout" class="logout-btn">Logout</button>
    <div class="dashboard-row">
      <div class="dashboard-column">
        <div class="dashboard-card" @click="startExperiment">
          <div class="card-title">Iniciar Experimento</div>
          <div class="card-description">Inicia los experimentos secuencialmente</div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import { ref } from 'vue';
import { auth } from '@/firebaseConfig';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const router = useRouter();

    const logout = () => {
      auth.signOut();
      router.push('/');
    };

    const startExperiment = () => {
      router.push(`/experiment/1`);
    };

    return { logout, startExperiment };
  }
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.logout-btn {
  padding: 10px;
  background-color: #ff5f5f;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-bottom: 20px;
}

.dashboard-row {
  display: flex;
  justify-content: center;
}

.dashboard-column {
  flex: 1 0 calc(33.333% - 20px);
}

.dashboard-card {
  cursor: pointer;
  background-color: #f0f0f0;
  border-radius: 10px;
  padding: 20px;
  transition: transform 0.2s;
  text-align: center;
}

.dashboard-card:hover {
  transform: translateY(-5px);
}

.card-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.card-description {
  font-size: 14px;
  color: #666;
}
</style>

