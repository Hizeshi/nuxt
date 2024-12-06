<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import { useAuthStore } from '~/stores/auth';
import { useRatingStore } from '~/stores/rating';
import { useReviewStore } from '~/stores/review';
import { useRouter } from 'vue-router';

const authStore = useAuthStore();
const ratingStore = useRatingStore();
const reviewStore = useReviewStore();
const router = useRouter();

const userName = computed(() => authStore.authData?.fio || 'Guest');
const userEmail = computed(() => authStore.authData?.email || 'Not available');
const userBirthday = computed(() => authStore.authData?.birthday || 'Not available');
const userGender = computed(() => authStore.authData?.gender || 'Not available');
const reviewCount = computed(() => authStore.authData?.reviewCount || 0);
const ratingCount = computed(() => authStore.authData?.ratingCount || 0);

const tabs = [
  { id: 'profile', label: 'MY PROFILE' },
  { id: 'reviews', label: 'MY REVIEWS' },
  { id: 'scores', label: 'MY SCORES' },
];

const activeTab = ref('profile');

const setActiveTab = (tab: string) => {
  activeTab.value = tab;
};

const deleteAccount = async () => {
  try {
    await authStore.deleteAccount();
    await router.push('/');
  } catch (e: any) {
    console.error("Ошибка при удалении аккаунта:", e.message);
  }
};

const navigateToEditProfile = () => {
  router.push('/editProfile');
};

const fetchUserReviews = async () => {
  try {
    await reviewStore.fetchReviewsByUserId(authStore.authData?.id);
    console.log("Загруженные отзывы:", reviewStore.reviews);
  } catch (e) {
    console.error("Ошибка при получении отзывов:", e);
  }
};

const fetchUserScores = async () => {
  try {
    await ratingStore.fetchRatingByUserId();
    console.log("Загруженные оценки:", ratingStore.ratings);
  } catch (e) {
    console.error("Ошибка при получении оценок:", e);
  }
};

const deleteReview = async (reviewId) => {
  try {
    await reviewStore.deleteReview(authStore.authData?.id, reviewId);
    fetchUserReviews();
  } catch (e) {
    console.error("Ошибка при удалении отзыва:", e.response ? e.response.data : e.message);
  }
};

const deleteRating = async (ratingId) => {
  try {
    await ratingStore.deleteRating(authStore.authData?.id, ratingId);
    fetchUserScores();
  } catch (e) {
    console.error("Ошибка при удалении оценки:", e.response ? e.response.data : e.message);
  }
};

onMounted(() => {
  if (authStore.authData?.id && authStore.authData?.token) {
    fetchUserReviews();
    fetchUserScores();
  }
});
</script>

<template>
  <div>
    <div class="text-section mb-3 fw-bold fs-3">
      <p>{{ userName }}</p>
      <p>COUNT SCORES: {{ ratingCount }}</p>
      <p>COUNT REVIEWS: {{ reviewCount }}</p>
    </div>

    <nav class="nav nav-tabs mt-4 d-flex mb-4">
      <button
          v-for="tab in tabs"
          :key="tab.id"
          class="nav-link text-center flex-grow-1"
          :class="{ active: activeTab === tab.id }"
          @click="setActiveTab(tab.id)"
      >
        {{ tab.label }}
      </button>
    </nav>

    <div v-show="activeTab === 'profile'" class="tab-pane show active fs-5">
      <div class="mb-3">
        <span class="fw-bold">EMAIL: </span>{{ userEmail }}
      </div>
      <div class="mb-3">
        <span class="fw-bold">BIRTHDAY: </span>{{ userBirthday }}
      </div>
      <div class="mb-3">
        <span class="fw-bold">GENDER: </span>{{ userGender }}
      </div>
      <button class="btn btn-success me-2" @click="navigateToEditProfile">USER</button>
      <button class="btn btn-danger" @click="deleteAccount">DELETE ACCOUNT</button>
    </div>

    <div v-show="activeTab === 'reviews'" class="tab-pane show">
      <div v-if="reviewStore.reviews.length > 0">
        <div class="card mb-3" v-for="review in reviewStore.reviews" :key="review.id">
          <div class="card-body">
            <h5 class="card-title">{{ review.film.name }}</h5>
            <p class="card-text">{{ review.message }}</p>
            <p class="card-text"><small class="text-muted">Date: {{ review.created_at }}</small></p>
            <button class="btn btn-danger" @click="deleteReview(review.id)">REMOVE</button>
          </div>
        </div>
      </div>
      <p v-else>No reviews yet.</p>
    </div>

    <div v-show="activeTab === 'scores'" class="tab-pane show">
      <div v-if="ratingStore.ratings.length > 0">
        <div class="card mb-3" v-for="score in ratingStore.ratings" :key="score.id">
          <div class="card-body">
            <h5 class="card-title">{{ score.film.name }}</h5>
            <p class="card-text">Score: {{ score.score }}</p>
            <p class="card-text"><small class="text-muted">Date: {{ score.created_at }}</small></p>
            <button class="btn btn-danger" @click="deleteRating(score.id)">REMOVE</button>
          </div>
        </div>
      </div>
      <p v-else>No scores yet.</p>
    </div>
  </div>
</template>

<style scoped>
.nav-link.active {
  background-color: #4fae1f !important;
  border-color: #4fae1f !important;
  color: white !important;
}

.btn-success {
  background-color: #4fae1f;
  border-color: #4fae1f;
}

.btn-success:hover {
  background-color: #4fae1f;
  border-color: #4fae1f;
}
</style>
