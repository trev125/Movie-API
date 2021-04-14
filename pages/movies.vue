<template>
  <div>
    <v-card v-for="movie in allMovieData" :key="movie.id">
      <v-img
        :src="'http://image.tmdb.org/t/p/w500' + movie.poster_path"
        width="200"
      ></v-img>

      <v-card-title class="headline"> {{ movie.title }} </v-card-title>

      <v-card-text>
        <v-row align="center" class="mx-0">
          <v-rating
            :value="movie.vote_average"
            length="10"
            color="amber"
            dense
            half-increments
            readonly
            size="14"
          ></v-rating>

          <div class="grey--text ml-4">
            {{ movie.vote_average }} ({{ movie.vote_count }})
          </div>
        </v-row>
        <v-row align="center" class="mx-0">
          <v-chip-group column>
            <v-chip
              v-for="genre in movie.genres"
              :key="genre.id"
              disabled
              class="my-4 subtitle-1"
            >
              <v-icon left> mdi-movie </v-icon>
              {{ genre.name }}
            </v-chip>
          </v-chip-group>
        </v-row>

        <div>{{ movie.overview }}</div>
        <v-btn
          elevation="2"
          :href="'https://www.imdb.com/title/' + movie.imdb_id"
          target="_blank"
          >View on IMDB</v-btn
        >
      </v-card-text>

      <v-divider class="mx-4"></v-divider>

      <!-- <div>{{ movie }}</div> -->

      <v-card v-if="movie.streaming_providers">
        <v-card-title> Streaming availability </v-card-title>
        <v-btn
          elevation="1"
          :href="movie.streaming_providers.link"
          target="_blank"
          >View on The Movie DB</v-btn
        >
        <v-divider class="mx-3"></v-divider>
        <v-card v-for="(value, name) in movie.streaming_providers" :key="name">
          <v-card v-if="name === 'flatrate'">
            <v-card-title> Availible to stream (no ads) </v-card-title>
            <v-chip-group column>
              <v-chip
                v-for="provider in value"
                :key="provider.provider_id"
                disabled
                class="my-4 subtitle-1"
              >
                <v-img
                  :src="'https://image.tmdb.org/t/p/w500' + provider.logo_path"
                  max-width="25"
                ></v-img>
                {{ provider.provider_name }}
              </v-chip>
            </v-chip-group>
          </v-card>
          <v-card v-if="name === 'ads'">
            <v-card-title> Availible to stream (with ads) </v-card-title>
            <v-chip-group column>
              <v-chip
                v-for="provider in value"
                :key="provider.provider_id"
                disabled
                class="my-4 subtitle-1"
              >
                <v-img
                  :src="'https://image.tmdb.org/t/p/w500' + provider.logo_path"
                  max-width="25"
                ></v-img>
                {{ provider.provider_name }}
              </v-chip>
            </v-chip-group>
          </v-card>
          <v-card v-if="name === 'buy'">
            <v-card-title> Availible for purchase </v-card-title>
            <v-chip-group column>
              <v-chip
                v-for="provider in value"
                :key="provider.provider_id"
                disabled
                class="my-4 subtitle-1"
              >
                <v-img
                  :src="'https://image.tmdb.org/t/p/w500' + provider.logo_path"
                  max-width="25"
                ></v-img>
                {{ provider.provider_name }}
              </v-chip>
            </v-chip-group>
          </v-card>
          <v-card v-if="name === 'rent'">
            <v-card-title> Availible for rent </v-card-title>
            <v-chip-group column>
              <v-chip
                v-for="provider in value"
                :key="provider.provider_id"
                disabled
                class="my-4 subtitle-1"
              >
                <v-img
                  :src="'https://image.tmdb.org/t/p/w500' + provider.logo_path"
                  max-width="25"
                ></v-img>
                {{ provider.provider_name }}
              </v-chip>
            </v-chip-group>
          </v-card>
        </v-card>
      </v-card>
    </v-card>
  </div>
</template>

<script>
const axios = require('axios')

export default {
  data() {
    return {
      hasData: false,
      hasSimilarMovies: false,
      allMovieData: [],
      movieId: [13368, 3083, 11934, 134, 155],
    }
  },
  created() {
    this.movieId.forEach((movie) => {
      this.getMovie(movie)
      this.getStreaming(movie)
    })
  },
  methods: {
    getMovie(id) {
      const vm = this
      axios
        .get(
          `https://api.themoviedb.org/3/movie/` +
            id +
            `?api_key=3c3685919ba529e9728a4603bd9cb0e9`
        )
        .then(function (response) {
          const tempObj = response.data
          vm.tempStreams = vm.getStreaming(id)
          tempObj.streaming_providers = vm.tempStreams
          // console.log(tempObj)
          vm.allMovieData.push(tempObj)
          // console.log(vm.allMovieData)
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
    },
    getStreaming(id) {
      const vm = this
      axios
        .get(
          'https://api.themoviedb.org/3/movie/' +
            id +
            '/watch/providers?api_key=3c3685919ba529e9728a4603bd9cb0e9'
        )
        .then(function (response) {
          vm.allMovieData.forEach((movie) => {
            if (movie.id === id) {
              movie.streaming_providers = response.data.results.US
            }
          })
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
    },
    // getSimilarMovies(id) {
    //   const vm = this
    //   axios
    //     .get(
    //       'https://api.themoviedb.org/3/movie/' +
    //         id +
    //         '/similar?api_key=3c3685919ba529e9728a4603bd9cb0e9'
    //     )
    //     .then(function (response) {
    //       // handle success
    //       // console.log(response.data)
    //       vm.hasSimilarMovies = true
    //       vm.movieData.similarMovies = []
    //       response.data.results.forEach((movie) => {
    //         movie.poster_path =
    //           'http://image.tmdb.org/t/p/w500' + movie.poster_path
    //         vm.movieData.similarMovies.push(movie)
    //       })
    //     })
    //     .catch(function (error) {
    //       // handle error
    //       console.log(error)
    //     })
    // },
  },
}
</script>
