<template>
  <div id="app">
    <Header @valueFilter="filter" :moviesFilters="moviesFilters" />
	<Loading v-if="loadingStatus"></Loading>
	<Error v-else-if="!loadingStatus && movies.length == 0" />
    <Main v-else :loadingStatus="loadingStatus" :movies="movies" />
  </div>
</template>

<script>
import Header from './components/Header.vue';
import Main from './components/Main.vue';
import Loading from './components/Loading.vue';
import Error from './components/Error.vue';
import axios from 'axios';

export default {
	name: 'app',
	components: {
		Header,
		Main,
		Loading,
		Error,
	}, 
	data () {
		return {
			movies: [],
			moviesFilters: [],
			url: {
                movies: 'https://admin.lmndev.ml/api/public/films?city_id=15',
                filters: 'https://admin.lmndev.ml/api/public/films/filters/15'
			},
			loadingStatus: true,
			finalUrl: '',
		};
	},
	methods: {
		filter(data) {
			this.getMovies(data.finalUrl);
		},
		getMovies(urlMovies) {
			this.loadingStatus = true;
			axios.get(urlMovies).then((response) => {
				this.movies = response.data;
				this.loadingStatus = false;
			});
			
		},
		getMoviesFilters(){
			axios.get(this.url.filters).then((response) => {
				this.moviesFilters = response.data;
			});
		}
	},
	mounted () {
		let linkToday = {
			finalUrl : this.url.movies + '&from_date_formatted=' + new Date().toISOString(),
		};
		this.filter(linkToday);
		this.getMoviesFilters();
	}
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=IBM+Plex+Sans:300,400,500,600,700&display=swap&subset=cyrillic');

* {
  margin: 0;
  padding: 0;
  font-family: IBM Plex Sans;
  font-style: normal;
}

.vdp-datepicker {
  input {
    font-weight: normal;
    font-size: 16px;
    line-height: 24px;
    color: #353535;
    width: 90%;
    border: none;
    outline: none;

    &::placeholder {
      color: #979797;
    }
  }
}
</style>
