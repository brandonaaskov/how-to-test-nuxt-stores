<template>
  <section>
    <h2 class="title">Disney Movies</h2>
    <ul class="movies">
      <li v-for="movie in disneyMovies" :key="movie.title">
        {{movie.title}}
      </li>
    </ul>
  </section>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters({
      byStudio: 'movies/byStudio'
    }),
    disneyMovies () {
      return this.byStudio('Disney')
    }
  }
}
</script>

<style>
.title {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}
</style>
