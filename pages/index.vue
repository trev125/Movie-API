<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline"> Welcome to the test site </v-card-title>
        <v-card-text>
          <v-btn elevation="2" @click="getMovie(13368)">White Christmas</v-btn>
          <v-btn elevation="2" @click="getMovie(3083)"
            >Mr. Smith Goes to Washington</v-btn
          >
          <v-btn elevation="2" @click="getMovie(11934)"
            >The Hudsucker Proxy</v-btn
          >
          <v-btn elevation="2" @click="getMovie(134)"
            >O Brother, Where Art Thou?</v-btn
          >
          <v-btn elevation="2" @click="getMovie(155)">The Dark Knight</v-btn>
          <p v-if="hasData">Title: {{ movieData.title }}</p>
          <p v-if="hasData">Overview: {{ movieData.overview }}</p>
          <p v-if="hasData">Release Date: {{ movieData.releaseDate }}</p>
          <v-img v-if="hasData" :src="movieData.poster"></v-img>
        </v-card-text>
      </v-card>
      <v-card v-if="hasSimilarMovies">
        <v-card-title class="headline"> Similar Movies </v-card-title>
        <v-card-text v-for="movie in movieData.similarMovies" :key="movie.id">
          <p>Title: {{ movie.title }}</p>
          <p>Overview: {{ movie.overview }}</p>
          <p>Release Date: {{ movie.release_date }}</p>
          <v-img :src="movie.poster_path"></v-img>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
const axios = require('axios')

export default {
  data() {
    return {
      hasData: false,
      hasSimilarMovies: false,
      movieData: {
        title: '',
        overview: '',
        releaseDate: '',
        poster: '',
        similarMovies: [],
      },
    }
  },
  methods: {
    getMovie(id) {
      this.getSimilarMovies(id)
      const vm = this
      axios
        .get(
          `https://api.themoviedb.org/3/movie/` +
            id +
            `?api_key=3c3685919ba529e9728a4603bd9cb0e9`
        )
        .then(function (response) {
          // handle success
          vm.hasData = true
          // console.log(response.data)
          vm.movieData.title = response.data.title
          vm.movieData.overview = response.data.overview
          vm.movieData.releaseDate = response.data.release_date
          vm.movieData.poster =
            'http://image.tmdb.org/t/p/w500' + response.data.poster_path
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
    },
    getSimilarMovies(id) {
      const vm = this
      axios
        .get(
          'https://api.themoviedb.org/3/movie/' +
            id +
            '/similar?api_key=3c3685919ba529e9728a4603bd9cb0e9'
        )
        .then(function (response) {
          // handle success
          // console.log(response.data)
          vm.hasSimilarMovies = true
          vm.movieData.similarMovies = []
          response.data.results.forEach((movie) => {
            movie.poster_path =
              'http://image.tmdb.org/t/p/w500' + movie.poster_path
            vm.movieData.similarMovies.push(movie)
          })
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
    },
  },
}
</script>
