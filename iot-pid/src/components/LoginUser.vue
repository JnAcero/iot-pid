<script setup>
import { auth } from "@/firebaseConfig";
import { signInWithEmailAndPassword, onAuthStateChanged } from "firebase/auth";
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";
import userModule from "@/modules/user.module";

const router = useRouter();
const { actions, state } = userModule;
const loginState = computed(() => state.loginState.value);
const email = ref("");
const password = ref("");

function login() {
  signInWithEmailAndPassword(auth, email.value, password.value)
    .then((userCredential) => {
      const user = userCredential.user;
      console.log("Successfully logged in", user);
      router.push('/dashboard');
    })
    .catch((error) => {
      alert(error.message);
    });
}

onMounted(() => {
  console.log("Loginnnn");
  actions.setLoginState(1);
  onAuthStateChanged(auth, (user) => {
    if (user) {
      actions.setCurrentUser(user);
      router.push('/dashboard');
    } else {
      actions.setLoginState(4);
      console.log("No hay nadie autenticado");
    }
  });
});
</script>

<template>
  <main class="container">
    <template v-if="loginState === 1">
      <h3>Cargando...</h3>
    </template>
    <template v-else-if="loginState === 3">
      <h3>Estás autenticado pero no registrado</h3>
    </template>
    <template v-else>
      <div class="login-form">
        <label for="email">Correo electrónico:</label>
        <input type="email" id="email" v-model="email">
        <label for="password">Contraseña:</label>
        <input type="password" id="password" v-model="password">
        <button @click="login">Iniciar sesión</button>
      </div>
    </template>
  </main>
</template>

<style scoped>
.container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.login-form {
  display: flex;
  flex-direction: column;
}

label {
  font-weight: bold;
  margin-bottom: 5px;
}

input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 10px;
}

button {
  padding: 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
