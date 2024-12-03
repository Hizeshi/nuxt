<script setup lang="ts">
</script>
<template>
  <div class="row">
    <div class="col-md-4"></div>
    <div class="col-md-4">
      <h1> Register </h1>
      <form action="#">
        <label for="fio" class="form-label">Full name</label>
        <input type="text" class="form-control" id="fio">
        <div class="mb-3">
        <label for="email" class="form-label">Email address</label>
        <input type="email" class="form-control" id="email">
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Password</label>
          <input type="password" class="form-control" id="password">
        </div>
        <div class="mb-3">
          <label for="birthday" class="form-label">Birthday</label>
          <input type="birthday" class="form-control" id="birthday">
        </div>

        <div class="mb-3">
          <label for="gender" class="form-label">Gender</label>
          <select class="form-select" aria-label="Default select example">
            <option selected>Gender</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
          </select>
        </div>
        <div>
          <button type="submit" class="btn btn-primary">SignUp</button>
        </div>
      </form>
    </div>
  </div>
</template>
