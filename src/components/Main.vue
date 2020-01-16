<template>
    <main>
        <div class="container">
            <div 
                v-for="movie in movies" 
                :key="movie.id" 
                class="movie-block"
            >
                <div class="poster">
                    <a :href="movie.trailer_hd" target="_blank">
                        <div class="play-btn">
                                <img class="" src="../assets/play_btn.svg" alt="" srcset="">
                        </div>
                        <img class="poster-img" :src="movie.url_poster" alt="">
                    </a>
                </div>
                <div class="description">
                    <span v-if="movie.is_premiere" class="premiere">
                        <img src="../assets/Premiere.svg" alt="">
                        Премьера
                    </span>
                    <p class="title">{{ movie.title }}</p>
                    <div class="genres">
                        <div 
                            v-for="(genre, index) in movie.genres" 
                            :key="genre.id" 
                            class="genre"
                        >
                            <span v-if="index != movie.genres.length-1">{{ genre.title | capitalize}},</span>
                            <span v-else>{{ genre.title | capitalize }}</span>
                        </div>
                    </div>
                </div>
                <div class="wrapper-times">
                    <div class="times">
                        <div 
                            v-for="time in movie.seances" 
                            :key="time.id" 
                            class="time"
                        >
                            <button> {{ time.time }} </button>
                            <span class="format-view"> {{time.formats[0].title}} </span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>
</template>

<script>
export default {
    name: 'Main',
    props: [
        'movies',
    ],
    filters: {
        capitalize: function (value) {
            if (!value) {
                return '';
            }
            value = value.toString();
            return value.charAt(0).toUpperCase() + value.slice(1);
        }
    }
}
</script>

<style lang="scss" scoped>
main {
    @media (max-width: 1200px) {
        background: #FFFFFF;
        margin-top: 16px;
    }
}

@media (min-width: 1200px) {
    .container {
        width: 1060px;
        margin: auto;
        margin-top: 50px;
    }

    .movie-block {
        display: flex;
        border-bottom: 1px solid #E2E2E2;
        padding: 30px;
    }
}

.movie-block {
    display: flex;
    flex-direction: column;
    border-bottom: 1px solid #E2E2E2;
    padding: 16px;
    @media (min-width: 576px) {
        padding: 30px;
        // flex-direction: row;
    }
    @media (min-width: 992px) {
        flex-direction: row;
    }
}

.poster {
    margin-right: 40px;
    display: flex;
    align-items: center;
    position: relative;
    @media (max-width: 576px) {
        width: 100%;
    }

    a {
        position: relative;
        
        .play-btn {
            position: absolute;
            width: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
        }

        .poster-img {
            width: 160px;
            height: 217px;
            box-shadow: inset 0px 0px 1px rgba(0, 0, 0, 0.14);
            border-radius: 8px;
        }
    }

    
    
}

.description {
    display: flex;
    flex-direction: column;
    justify-content: center;
    

    @media (min-width: 576px) {
        max-width: 350px;
    }

    p {
        padding: 10px 0;
    }

    .premiere {
        font-weight: normal;
        font-size: 14px;
        line-height: 16px;
        letter-spacing: 2px;
        text-transform: uppercase;
        color: #FBCA12;

        img {
            margin-right: 5px;
        }

        @media (max-width: 992px) {
            margin-top: 22px;
        }
    }

    .title {
        font-weight: 500;
        font-size: 32px;
        line-height: 40px;
        color: #353535;
        
    }

    .genres {
        display: flex;
        flex-direction: row;
        font-weight: normal;
        font-size: 14px;
        line-height: 20px;
        color: #979797;
        flex-wrap: wrap;
    }

    .genre {
        padding-right: 5px;
    }

    
}

.wrapper-times {
    display: flex;
    @media (min-width: 992px) {
        flex: 1;
        display: flex;
        justify-content: flex-end;
    }
}

.times {
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;
    height: min-content;
    width: 304px;
    
    .time {
        display: flex;
        flex-direction: column;
        margin-left: 16px;
        margin-top: 16px;
        height: 60px;

        &:nth-child(4n + 1), &:nth-child(1) {
            margin-left: 0;
        }

        button {
            width: 60px;
            height: 32px;
            outline: none;
            border: none;
            background: #FBCA12;
            box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.1);
            border-radius: 4px;
            font-weight: 500;
            font-size: 14px;
            line-height: 20px;
            color: #353535;
            cursor: pointer;
        }

        .format-view {
            font-weight: normal;
            font-size: 12px;
            line-height: 16px;
            text-align: center;
            color: #979797;
            margin-top: 10px;
        }
    }
}




</style>