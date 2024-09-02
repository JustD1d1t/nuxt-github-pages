<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'nuxt/app';
import axios from "axios"

const isLoading = ref(true);
const error = ref(null);
const success = ref(false);

const route = useRoute();
const router = useRouter();

onMounted(async () => {
  const oobCode = route.query.oobCode;

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
});
</script>
<template>
  <div>
    <h1>Weryfikacja adresu e-mail</h1>
    <div v-if="isLoading">Trwa weryfikacja...</div>
    <div v-if="error">{{ error }}</div>
    <div v-if="success">Adres e-mail został zweryfikowany pomyślnie!</div>
  </div>
</template>