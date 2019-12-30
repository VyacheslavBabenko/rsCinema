<template>
    <header>
        <div class="container">
            <div class="menu">  
                <div class="radio-btn">
                    <input 
                        @click="changeEventInput(today, 'today')" 
                        :checked="checkedToday" 
                        type="radio" 
                        value="today" 
                        name="today-tomorrow" 
                        id="today"
                    >
                    <label for="today">Сегодня </label>
                </div>
                <div class="radio-btn">
                    <input 
                        @click="changeEventInput(tomorrow, 'tomorrow')" 
                        :checked="checkedTomorrow" type="radio" 
                        value="tomorrow" 
                        name="today-tomorrow" 
                        id="tomorrow"
                    >
                    <label for="tomorrow">Завтра</label>
                </div>
                <div class="datepicker">
                    <datepicker  
                        :disabled-dates="disabledDates"
                        placeholder="Другая дата"
                        :language="ru" 
                        :monday-first="true"
                        :format="format"
                        v-model="calendar"
                        @input="changeEventInput(calendar, 'calendar')"
                    />
                    <img 
                        v-if="checkedCalendar == false" 
                        src="../assets/Calendar-gray.svg" 
                        alt=""
                    >
                    <img 
                        v-else 
                        src="../assets/Calendar.svg" 
                        alt=""
                    >
                </div>
                <div class="select-btn">
                    <select name="time" id="time">
                        <option value="-1" hidden="">Время сеанса</option>
                        <option 
                            v-for="(session, index) in sessions" 
                            v-bind:key="index" 
                            :value="session"
                        >
                        {{ session }}
                        </option>
                    </select>
                </div>
                <div class="select-btn">
                    <select 
                        v-model="genres" 
                        @change="getGenres(genres)" 
                        name="time" 
                        id="time"
                    >
                        <option value="-1" hidden="">Жанр</option>
                        <option 
                            v-for="(genre, index) in moviesFilters.genres" 
                            :key="index" 
                            :value="genre.id"
                        >
                        {{ genre.title }}
                        </option>
                    </select>
                </div>
                <div class="select-btn">
                    <select 
                        v-model="ages" 
                        @change="getAges(ages)" 
                        name="time" 
                        id="time"
                    >
                        <option value="-1" hidden="">Возраст</option>
                        <option 
                            v-for="(age, index) in moviesFilters.age_ratings" 
                            :key="index" 
                            :value="age"
                        >
                        {{ age }}
                        </option>
                    </select>
                </div>
                <div class="discard-btn">
                    <button 
                        :disabled="statusDiscardBtn" 
                        @click="discardAll()"
                    >
                        Сбросить 
                        <img 
                            v-if="statusDiscardBtn" 
                            src="../assets/cross_gray.svg" 
                            alt=""
                        >
                        <img 
                            v-else 
                            src="../assets/cross.svg" 
                        alt=""
                        >
                    </button>
                </div>
            </div>

        </div>
    </header>
</template> 

<script>
import Datepicker from 'vuejs-datepicker';
import {ru} from 'vuejs-datepicker/dist/locale';

export default {
    
    name: 'header-cinema', 
    components:{
        Datepicker
    },
    data () {
        return {
            ru: ru,
            format: 'd MMMM',
            sessions: ['Утренний', 'Дневной', 'Вечерний', 'Ночной'],
            genres: -1,
            ages: -1,
            checkedToday: true,
            checkedTomorrow: false,
            checkedCalendar: false,
            today: new Date(),
            tomorrow: new Date(new Date().getTime() + 24 * 60 * 60 * 1000),
            calendar: null, 
            disabledDates: {
                to: new Date(new Date().getTime() - 24 * 60 * 60 * 1000), 
            },
            basicUrl: 'https://admin.lmndev.ml/api/public/films?city_id=15',
            statusDiscardBtn: true,
            urlDay: '',
            urlGenres: '',
            urlAge: '',
        };
    },
    props: [
        'moviesFilters',
    ],
    methods: {
        getGenres(value) {
            this.urlGenres = '&genre=' + value;
            this.statusDiscardBtn = false;
        },
        getAges(value) {
            this.urlAge = '&age_rating=' + value.slice(0, -1);
            this.statusDiscardBtn = false;
        },
        changeEventInput(value, typeDay) {
            if (typeDay == 'today') {
                this.checkedToday = true;
                this.checkedCalendar = true;
                this.calendar = this.today;
            } else if (typeDay == 'tomorrow') {
                this.checkedTomorrow = true;
                this.checkedToday = false;
                this.checkedCalendar = true;
                this.calendar = this.tomorrow;
            } else {
                this.checkedToday = false;
                this.checkedTomorrow = false;
                this.checkedCalendar = true;
            }
            this.statusDiscardBtn = false;
            this.urlDay = '&from_date_formatted=' + value.toISOString();
        },
        discardAll() {
            this.genres = -1;
            this.urlGenres = '';
            this.ages = -1;
            this.urlAge = '';
            this.calendar = this.today;
            this.checkedToday = true;
            this.statusDiscardBtn = true;
        }
    },
    updated () {
        let finalUrl = this.basicUrl + this.urlDay + this.urlGenres + this.urlAge;
        this.$emit('valueFilter', {
            finalUrl
        });
    }

}
</script>

<style lang="scss" scoped>
header {
    background: linear-gradient(180deg, #2E2E2E 50%, rgba(255, 255, 255, 0) 50%, rgba(255, 255, 255, 0) 50%);
    height: 176px;
    position: relative;
    top: 0;
}
.container {
    width: 1060px;
    margin: auto;
    padding-top: 56px;
}

.menu {
    height: 56px;
    padding: 8px;
    background: #FFFFFF;
    box-shadow: 0px 2px 4px rgba(53, 53, 53, 0.08);
    border-radius: 8px;
    display: flex;
    align-items: center;
}

.radio-btn { 
    margin-right: 8px;
    input {
        display: none;
    }

    input[type="radio"]:checked+label {
        background: #FBCA12;
        box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.08);
        border-radius: 4px;
        color: #353535;
        font-weight: 500;
        font-size: 16px;
        line-height: 20px;
    }

    label { 
        font-weight: normal;
        font-size: 16px;
        line-height: 24px;
        border-radius: 4px;
        color: #979797;
        width: 88px;
        height: 48px;
        display: flex;
        align-items: center;
        text-align: center;
        justify-content: center;
        cursor: pointer;
    }
}

.datepicker {
    width: 143px;
    height: 56px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-right: 8px;
}

.select-btn {
    border-left: 1px solid #E2E2E2;
    height: 100%;
    display: flex;
    justify-content: center;
    
    select {
        outline: none;
        border: none;
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
        width: 192px;
        font-weight: normal;
        font-size: 16px;
        line-height: 24px;
        color: #979797;
        background: url('../assets/Chevron-bottom.svg') 95%/4% no-repeat #FFFFFF;
        padding-left: 20px;
    }
}

.discard-btn {
    button {
        outline: none;
        border: none;
        background: #FBCA12;
        box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.1);
        border-radius: 4px;
        width: 136px;
        height: 48px;
        font-weight: 500;
        font-size: 16px;
        line-height: 20px;
        cursor: pointer;
        color: #353535;

        &:disabled {
            background: #F6F6F6;
            box-shadow: none;
            cursor: default;
            color: #979797;
        }
    }
    img {
        padding-left: 10px;
    }
}
</style>