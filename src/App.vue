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
			finalUrl : this.url.movies,
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

#app {
	background: #FCFCFC;
	@media (max-width: 1200px) {
		background: #F6F6F6;
	}
}



// стили для datepicker

.vdp-datepicker {
	text-align: right !important;
	

	input {
		font-weight: normal;
		font-size: 16px;
		line-height: 24px;
		color: #979797;
		width: 90%;
		border: none;
		outline: none;
		cursor: pointer;

		@media (min-width: 1200px) {
			background: url('./assets/Calendar-gray.svg') 90% no-repeat #FFFFFF;
		}

		&::placeholder {
			color: #979797;
		}
	}

	.active-input {
		background: #FBCA12;
		font-weight: 500;
		font-size: 16px;
		line-height: 20px;
		color: #353535;
		padding-right: 10px;

		@media (min-width: 1200px) {
			background: url('./assets/Calendar.svg') 90% no-repeat #FBCA12;
			padding-right: 0;
		}

		&::placeholder {
			color: #353535;
		}
	}
   
	.vdp-datepicker__calendar {
		width: 320px;
		height: auto;
		background: #FFFFFF;
		box-shadow: 0px 8px 24px rgba(53, 53, 53, 0.16);
		border-radius: 0px 0px 8px 8px;
		border: none;
		padding-bottom: 24px;
		position: absolute;
		left: -100px;
		top: 48px;
		z-index: -1;

		@media (max-width: 1200px) {
			width: 314px;
			top: 48px;
			right: -45px;
			z-index: 1;
			left: auto;
		}

		header {
			display: flex;
			justify-content: center;
			margin-bottom: 16px;
			margin-top: 25px;
			.prev, .next {
				display: none;
			}

			.day__month_btn.up {
				
				font-weight: 500;
				font-size: 16px;
				line-height: 20px;
				color: #353535;
				cursor: default;

				&:hover {
					color: #353535;
					background: none;
				}
			}
		}

		header + div {
			display: flex;
			flex-wrap: wrap;
			padding-left: 16px;
			padding-right: 24px;
		}

		.cell {
			width: 32px;
			height: 24px;
			margin-top: 8px;
			margin-left: 8px;
			display: flex;
			justify-content: center;
			align-items: center;
			padding: 0;
			border: none;

			&.day {

				font-weight: 500;
				font-size: 12px;
				line-height: 12px;

				&.selected {
					background: #FBCA12;
					box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.08);
					border-radius: 4px;
				}

				&.disabled {
					color: rgba(53, 53, 53, 0.2);
				}

				&:not(.blank):not(.disabled):hover {
					background: #F6F6F6;
					border-radius: 4px;
					border: none;
				}
			}

			&.day-header {
				display: none;
			}
		}
	}
}
</style>
