<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h1>Raza.vue</h1>
        <p>{{ $route.params.raza }}</p>
        <hr>
        <hr>
        <nuxt-link to="/" no-prefetch>Index</nuxt-link>
      </div>
    </div>
  </div>
</template>

<script>
</script>
