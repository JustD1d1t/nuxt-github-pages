<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'nuxt/app';
import axios from "axios"

const isLoading = ref(true);
const error = ref(null);
const success = ref(false);

const route = useRoute();
const router = useRouter();
const incomeMode = ref(null);

const password = ref('');
const confirmPassword = ref('');

const changePassword = async () => {
  if (password.value !== confirmPassword.value) {
    error.value = 'Hasła nie są takie same.';
    return;
  }
  try {
    // Wyślij `oobCode` i `password` do backendu
    const response = await axios.post('https://fo-mobile-backend-6c319973e933.herokuapp.com/user/reset-password', { oobCode: route.query.oobCode, password: password.value });
    success.value = response.data.message;
    error.value = '';
  } catch (err) {
    error.value = 'Błąd podczas zmiany hasła. Spróbuj ponownie później.';
    console.error(err);
  } finally {
    isLoading.value = false;
  }
};

onMounted(async () => {
  const { oobCode, mode } = route.query;
  incomeMode.value = mode;
  if (mode === 'verifyEmail')  {
    if (oobCode) {
      try {
        // Wyślij `oobCode` do backendu
        const response = await axios.post('https://fo-mobile-backend-6c319973e933.herokuapp.com/user/verify-email', { oobCode });
        success.value = response.data.success;
      } catch (err) {
        error.value = 'Błąd podczas weryfikacji e-maila. Spróbuj ponownie później.';
        console.error(err);
      } finally {
        isLoading.value = false;
      }
    } else {
      error.value = 'Nieprawidłowy kod weryfikacyjny.';
      isLoading.value = false;
    }
  }

});
</script>
<template>
  <div v-if="incomeMode === 'verifyEmail'">
    <h1>Weryfikacja adresu e-mail</h1>
    <div v-if="isLoading">Trwa weryfikacja...</div>
    <div v-if="error">{{ error }}</div>
    <div v-if="success">Adres e-mail został zweryfikowany pomyślnie!</div>
  </div>
  <div v-if="incomeMode === 'resetPassword'">
    <h1>Zmiana hasła</h1>
    <input type="password" v-model="password" placeholder="Nowe hasło">
    <input type="password" v-model="confirmPassword" placeholder="Powtórz hasło">
    <div v-if="error">{{ error }}</div>
    <div v-if="success">Hasło zmienione pomyślnie</div>
    <button @click="() => changePassword()">Zmień hasło</button>
  </div>
</template>