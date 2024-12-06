<script setup lang="ts">
import { useAuthStore } from "~/stores/auth";
import { useRouter } from "vue-router";

const authStore = useAuthStore();
const router = useRouter();

const logout = async () => {
  await authStore.signout();
};
</script>

<template>
  <nav class="navbar navbar-expand-lg bg-body-tertiary shadow-sm">
    <div class="container-fluid">
      <NuxtLink to="/" class="navbar-brand text-danger fw-bold">KinoTower</NuxtLink>
      <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse justify-content-end" id="navbarSupportedContent">
        <div class="d-flex align-items-center gap-3">
          <template v-if="!authStore.authData">
            <button
                class="btn btn-outline-success"
                type="button"
                @click="$router.push('/signup')"
            >
              Sign Up
            </button>
            <button
                type="button"
                class="btn btn-outline-info"
                @click="$router.push('/signin')"
            >
              Sign In
            </button>
          </template>
          <template v-else>
            <NuxtLink to="/" class="btn btn-outline-primary" @click="$router.push('/profile/')">Profile</NuxtLink>
            <button
                @click="logout"
                type="button"
                class="btn btn-outline-danger"
            >
              Sign Out
            </button>
          </template>
        </div>
      </div>
    </div>
  </nav>
  <div class="container mt-3">
    <slot />
  </div>
  <footer class="bg-dark text-white p-3 mt-3">
    <div class="container text-center">
      <p class="lead"> &copy; Aboba</p>
    </div>
  </footer>
</template>

<style scoped>
.navbar-brand {
  font-size: 1.75rem;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  color: #343a40;
  font-family: "Arial Black", sans-serif;
}

.btn-outline-success {
  border-color: #4fae1f;
  color: #4fae1f;
}

.btn-outline-success:hover {
  color: white;
  background-color: #4fae1f;
}

.btn-outline-info {
  border-color: #4fae1f;
  color: #4fae1f;
}

.btn-outline-info:hover {
  color: white;
  background-color: #4fae1f;
}

.btn-outline-danger {
  border-color: #dc3545;
  color: #dc3545;
}

.btn-outline-danger:hover {
  color: white;
  background-color: #dc3545;
}

.btn-outline-primary {
  border-color: #4fae1f;
  color: #4fae1f;
}

.btn-outline-primary:hover {
  color: white;
  background-color: #4fae1f;
}

.container {
  padding: 1.5rem;
}

footer {
  background-color: #343a40;
  color: white;
  padding: 1.5rem;
  text-align: center;
}

footer .lead {
  margin: 0;
  font-size: 1rem;
}

.shadow-sm {
  box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.075) !important;
}
</style>


