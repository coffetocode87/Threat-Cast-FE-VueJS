<template>
  <div class="login">
    <h2>Threat Dashboard Login</h2>

    <input v-model="username" placeholder="Username" />
    <input v-model="password" type="password" placeholder="Password" />

    <button @click="login" :disabled="loading">
      {{ loading ? "Logging in..." : "Login" }}
    </button>

    <p v-if="error" class="error">{{ error }}</p>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import api from "../api/axios";

const router = useRouter();

const username = ref("");
const password = ref("");
const loading = ref(false);
const error = ref("");

const login = async () => {
  try {
    loading.value = true;
    error.value = "";

    console.log("Request login dikirim...");

    const res = await api.post("/auth/login", {
      username: username.value,
      password: password.value,
    });

    console.log("Response:", res);

    localStorage.setItem("token", res.data.token);

    console.log("Token disimpan:", res.data.token);

    router.push("/dashboard");
  } catch (err) {
    console.error("LOGIN ERROR:", err);

    error.value =
      err.response?.data?.message ||
      err.message ||
      "Login failed. Check username/password.";
  } finally {
    loading.value = false;
  }
};
</script>

<style>
.login {
  max-width: 350px;
  margin: 100px auto;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.error {
  color: red;
}
</style>
