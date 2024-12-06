<script setup lang="ts">
import { ref } from 'vue';
import { useAuthStore } from '~/stores/auth';
import { useGenderStore } from '~/stores/gender';
import { useRouter } from 'vue-router';

const authStore = useAuthStore();
const genderStore = useGenderStore();
const router = useRouter();

const fio = ref(authStore.authData?.fio || '');
const email = ref(authStore.authData?.email || '');
const birthday = ref(authStore.authData?.birthday || '');
const gender = ref(authStore.authData?.gender || 0);
const errorMessage = ref('');

const updateUser = async () => {
  if (!fio.value || !email.value || !birthday.value || gender.value === 0) {
    errorMessage.value = "Все поля обязательны для заполнения.";
    return;
  }

  const updateData = {
    fio: fio.value,
    email: email.value,
    birthday: birthday.value,
    gender_id: gender.value
  };

  try {
    await authStore.updateUser(updateData);
    router.push('/profile');
  } catch (e: any) {
    errorMessage.value = e.response ? e.response.data.message : e.message;
  }
};
</script>

<template>
  <div class="row">
    <div class="col-md-4"></div>
    <div class="col-md-4">
      <h1 align="center">Edit Profile</h1>
      <form @submit.prevent="updateUser">
        <div class="mb-3">
          <label for="fio" class="form-label">Full name</label>
          <input type="text" class="form-control" id="fio" v-model="fio" required>
        </div>

        <div class="mb-3">
          <label for="email" class="form-label">Email address</label>
          <input type="email" class="form-control" id="email" v-model="email" required>
        </div>

        <div class="mb-3">
          <label for="birthday" class="form-label">Birthday</label>
          <input type="date" class="form-control" id="birthday" v-model="birthday" required>
        </div>

        <div class="mb-3">
          <label for="gender" class="form-label">Gender</label>
          <select class="form-select" v-model="gender" required>
            <option value="0" selected>Gender</option>
            <option
                v-for="gender in genderStore.genders"
                :key="gender.id"
                :value="gender.id">{{ gender.name }}</option>
          </select>
        </div>

        <div>
          <button type="submit" class="btn btn-primary">Update</button>
        </div>
      </form>
      <div class="alert alert-danger" role="alert" v-if="errorMessage">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>