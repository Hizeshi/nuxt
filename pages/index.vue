<script setup lang="ts">
import {useFilmStore} from "~/stores/film";
import {useCategoryStore} from "~/stores/category";
import {useCountryStore} from "~/stores/country";

const categoryStore = useCategoryStore();
const filmStore = useFilmStore()
const countryStore = useCountryStore();
const category = ref(null);
const country = ref(null);
const sort = ref('name');

const filter = () => {
  filmStore.addCategoryToParams(category.value);
  filmStore.addCountryToParams(country.value);
  filmStore.addSortToParams(sort.value);
  filmStore.currentPage = 1;
  filmStore.fetchFilms();
}
const reset = () => {
  category.value = null;
  country.value = null;
  sort.value = 'name';
  filter();
}
const goto = (page: number) => {
  page = (page < 1) ? 1 : page;
  page = (page >filmStore.countPages) ? filmStore.countPages : page;
  if (page <= filmStore.countPage && page > 0) {
    filmStore.currentPage = page;
    filmStore.fetchFilms();
  }
}


</script>

<template>
  <div class="row mt-2">
    <div class="col-md-4">
      <select v-model="category" @change="filter" class="form-select" aria-label="Default select example">
        <option :value="null" selected>Select genre</option>
        <option v-for="category in categoryStore.categories"
                :key="category.id"
                :value="category.id">
          {{ category.name }} {{ category.filmCount }}
        </option>
      </select>
    </div>
    <div class="col-md-4">
      <select v-model="country" @change="filter" class="form-select" aria-label="Default select example">
        <option :value="null" selected>All Countries</option>
        <option
            v-for="country in countryStore.countries"
            :key="country.id"
            :value="country.id">
          {{ country.name }}
        </option>
      </select>
    </div>
    <div class="col-md-2">
      <select v-model="sort" @change="filter" class="form-select" aria-label="Default select example">
        <option value="1">Name</option>
        <option value="2">Year</option>
        <option value="3">Rating</option>
      </select>
    </div>
    <div class="col-md-2">
      <button type="button" class="btn btn-outline-danger" @click="reset">Reset</button>
    </div>
  </div>
  <div v-if="!filmStore.isLoading" class="row row-cols-1 row-cols-md-3 g-4 mt-2">
    <div class="col" v-for="film in filmStore.films" :key="film.id">
      <div class="card h-100">
        <img v-if="film.link_img" :src="film.link_img" class="card-img-top" alt="">
        <img v-else="film.link_img" src="https://m.media-amazon.com/images/I/71KPnecVSNL._SL1108_.jpg"
             class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title"> {{ film.name }}</h5>
          <p class="card-text"> {{ film.raringAvg }}</p>
          <p class="card-text"> {{ film.duration }} min.</p>
          <p class="card-text"> {{ film.year_of_issue }} year</p>
          <p class="card-text">
            <template v-for="(category, index) in film.categories" :key="category.id">
              {{ category.name + (index + 1 < film.categories.length ? ", " : "") }}
            </template>
          </p>
        </div>
        <button @click="$router.push(`/film/` + film.id)" type="button" class="btn btn-success"> View </button>
      </div>
    </div>
  </div>
  <div v-else class="d-flex justify-content-center mt-4">
    <div class="spinner-border" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
  </div>
  <nav class="mt-4 d-flex justify-content-center" aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item">
        <a class="page-link" @click.prevent="goto(filmStore.currentPage - 1)" aria-label="Previous">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li v-for="num in filmStore.countPage" :key="num"
          :class="['page-item', { 'active': filmStore.currentPage === num }]"
          @click.prevent="goto(num)">
        <a class="page-link" href="#">{{ num }}</a>
      </li>
      <li class="page-item">
        <a class="page-link" @click.prevent="goto(filmStore.currentPage + 1)" aria-label="Next">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.card-img-top {
  height: 500px;
  object-fit: cover;
}
</style>