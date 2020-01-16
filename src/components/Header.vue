<template>
    <header>
        <div class="container">
            
            <div 
                class="menu" 
                :class="{ hide : !statusMobFilterBtn }"
            >  
               <div class="days-picker">
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
                <div 
                    class="datepicker" 
                    :class="{'active-calendar' : activeCalendar}"
                >
                    <datepicker  
                        :disabled-dates="disabledDates"
                        placeholder="Другая дата"
                        :language="ru" 
                        :monday-first="true"
                        :full-month-name="true"
                        :format="format"
                        v-model="calendar"
                        @input="changeEventInput(calendar, 'calendar')"
                        :minimumView="'day'" 
                        :maximumView="'day'"
                        :input-class="{'active-input': activeCalendar}"
                    />
                </div>
               </div>
                <div class="select-btn">
                    <Select 
                        @input="getTimeSession" 
                        :options="sessions"
                        :defaultOptions="sessionsDefault"
                        :key="refreshFilters"
                    />
                </div>
                <div class="select-btn">
                    <Select 
                        @input="getGenres" 
                        :key="refreshFilters" 
                        :defaultOptions="genresDefault"
                        :options="moviesFilters.genres"
                    /> 
                </div>
                <div class="select-btn">
                    <Select 
                        @input="getAges" 
                        :key="refreshFilters" 
                        :defaultOptions="agesDefault"
                        :options="moviesFilters.age_ratings"
                    /> 
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
                            alt="иконка крестика"
                        >
                        <img 
                            v-else 
                            src="../assets/cross.svg" 
                            alt="иконка крестика"
                        >
                    </button>
                </div>
            </div>
            <div class="hide-btn">
                <button 
                    :class="{ disabled : statusMobFilterBtn }" 
                    @click="mobFilterBtn"> {{ textMobFilterBtn }} 
                </button>
            </div>
        </div>
    </header>
</template> 

<script>
import Datepicker from 'vuejs-datepicker';
import {ru} from 'vuejs-datepicker/dist/locale';
import moment from 'moment';
import Select from '../components/Select.vue';

export default {
    
    name: 'header-cinema', 
    components:{
        Datepicker,
        Select,
    },
    data () {
        return {

            // datepicker
            
            ru: ru,
            format: 'd MMMM',
            calendar: null, 
            disabledDates: {
                to: new Date(new Date().getTime() - 24 * 60 * 60 * 1000), 
            },

            // Фильтры 
            
            sessions: ['Утренний', 'Дневной', 'Вечерний', 'Ночной'],
            sessionsDefault: 'Время сеанса',
            genresDefault: 'Жанр',
            agesDefault: 'Возраст',
            urlGenres: '',
            urlAge: '',
            urlDay: this.construnctorDayUrl(moment()),
            textMobFilterBtn: 'Фильтр',
            statusMobFilterBtn: false,
            checkedToday: true,
            checkedTomorrow: false,
            activeCalendar: false,
            refreshFilters: false,
            today: moment(),
            tomorrow: moment().add(1, 'day').set({
                'hour' : 6, 
                'minute' : 0, 
                'second' : 0
            }),
            selectedDay: moment(),
            basicUrl: 'https://admin.lmndev.ml/api/public/films?city_id=15',
            statusDiscardBtn: true,
           
        };
    },
    props: [
        'moviesFilters',
    ],
    filters: {
        makeGenresArray(value) {
            let temp = [];
            if (value) {
                temp = value.map((name) => {
                    return name.title;
                });
            }
            return temp;
        },
    },
    methods: {
        mobFilterBtn() {
            this.statusMobFilterBtn = !this.statusMobFilterBtn;
            if (!this.statusMobFilterBtn) {
                this.textMobFilterBtn = 'Фильтр';
            } else {
                this.textMobFilterBtn = 'Свернуть фильтр';
            }
        },
        getGenres(value) {
            this.urlGenres = '&genre=' + value;
            this.statusDiscardBtn = false;
            this.finalConstructor();
        },
        getAges(value) {
            this.urlAge = '&age_rating=' + value.slice(0, -1);
            this.statusDiscardBtn = false;
            this.finalConstructor();
        },
        getTimeSession(value) { // функция выбора времени сеанса
            if (this.selectedDay.get('hour') < 6) {
                this.selectedDay.subtract(1000, 'year');
            }
            let startTime = moment(this.selectedDay);
            let endTime = moment(this.selectedDay);

            if (value == 'Утренний') {
                startTime.set({ 
                    'hour' : 9, 
                    'minute' : 0, 
                    'second' : 0,
                });
                endTime.set({ 
                    'hour' : 12, 
                    'minute' : 0, 
                    'second' : 0,
                });
            } else if (value == 'Дневной') {
                startTime.set({ 
                    'hour' : 12, 
                    'minute' : 0, 
                    'second' : 0,
                });
                endTime.set({ 
                    'hour' : 17, 
                    'minute' : 0, 
                    'second' : 0,
                });
            } else if (value == 'Вечерний') {
                startTime.set({ 
                    'hour' : 17, 
                    'minute' : 0, 
                    'second' : 0,
                });
                endTime.set({ 
                    'hour' : 22, 
                    'minute' : 0, 
                    'second' : 0,
                });
            } else if (value == 'Ночной') {
                startTime.set({ 
                    'hour' : 22, 
                    'minute' : 0, 
                    'second' : 0,
                });
                endTime.add(1, 'day').set({ 
                    'hour' : 6, 
                    'minute' : 0, 
                    'second' : 0,
                });
            }

            let urlTimeSession = {
                startTime : startTime,
                endTime : endTime,
            };
            this.urlDay = this.construnctorDayUrl(urlTimeSession);
            this.statusDiscardBtn = false;

            this.finalConstructor();
        },
        construnctorDayUrl (day){ // конструктор url
            let urlDayFrom;
            let urlDayTo;

            if (moment.isMoment(day)) {
                if (day.get('hour') < 6 ) {
                    urlDayFrom = '&from_date_formatted=' + day.utc(true).format();
                    urlDayTo = '&to_date_formatted=' + day.set({'hour' : 5, 'minute' : 59, 'second' : 59}).utc(true).format();
                } else {
                    urlDayFrom = '&from_date_formatted=' + day.utc(true).format();
                    urlDayTo = '&to_date_formatted=' + day.add(1, 'day').set({'hour' : 5, 'minute' : 59, 'second' : 59}).utc(true).format();
                }
                
            } else {
                urlDayFrom = '&from_date_formatted=' + moment(day.startTime).utc(true).format();
                urlDayTo = '&to_date_formatted=' + moment(day.endTime).utc(true).format();
            }
            return urlDayFrom + urlDayTo;
        },
        changeEventInput(value, typeDay) { // фунция выбора дня
            if (typeDay == 'today') {
                this.checkedToday = true;
                this.activeCalendar = false;
                this.selectedDay = moment(value);

                if (this.today.get('hour') < 6) {
                    this.calendar = new Date(moment().subtract(1, 'day'));
                } else {
                    this.calendar = new Date(this.today);
                }
            } else if (typeDay == 'tomorrow') {
                this.checkedTomorrow = true;
                this.checkedToday = false;
                this.activeCalendar = false;
                
                if (this.today.get('hour') < 6) {
                    this.calendar = new Date(this.today);
                    this.selectedDay = moment(value).subtract(1, 'day');
                } else {
                    this.calendar = new Date(this.tomorrow);
                    this.selectedDay = moment(value);
                }
            } else {
                this.checkedToday = false;
                this.checkedTomorrow = false;
                this.activeCalendar = true;
                this.selectedDay = moment(value).set({
                    'hour' : 6, 
                    'minute' : 0, 
                    'second' : 0
                });
            }

            this.statusDiscardBtn = false;
            this.urlDay = this.construnctorDayUrl(moment(this.selectedDay));
        
            this.disacardAllNotDate();
        },
        disacardAllNotDate () {
            this.urlGenres = '';
            this.urlAge = '';
            this.statusDiscardBtn = true;
            this.refreshFilters = !this.refreshFilters;
            
            this.finalConstructor();
        },
        discardAll() {
            this.urlGenres = '';
            this.urlAge = '';
            this.calendar = '';
            this.activeCalendar = false;
            this.checkedToday = true;
            this.statusDiscardBtn = true;
            this.urlDay = '';
            this.refreshFilters = !this.refreshFilters;

            this.finalConstructor();
        },
        finalConstructor() {
            let finalUrl = this.basicUrl + this.urlDay + this.urlGenres + this.urlAge;

            this.$emit('valueFilter', {
                finalUrl
            });
        }
    },
    mounted () {
        this.finalConstructor();
    }

}
</script>

<style lang="scss" scoped>



header {
    background: #FFFFFF;
    z-index: 1;
}

.menu {
    align-items: center;
    display: flex;
    flex-direction: column;
    width: 100%;

    &.hide {
        @media (max-width: 1200px) {
            display: none;
        }
    }

    .days-picker {
        display: flex;
        align-items: center;
        
        @media (max-width: 1200px) {
            width: 288px;
            padding: 16px;
        }
    }
}

@media  (min-width: 1200px) {
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
        flex-direction: row;
        align-items: center;
    }
}

.hide-btn {
    width: 100%;
    padding: 16px 0px;
    display: flex;
    align-items: center;
    justify-content: center;

    @media (min-width: 1200px) {
        display: none;
    }

    button {
        outline: none;
        border: none;
        background: #FBCA12;
        box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.1);
        border-radius: 4px;
        width: 288px;
        height: 48px;
        font-weight: 500;
        font-size: 16px;
        line-height: 20px;
        cursor: pointer;
        color: #353535;

        &.disabled {
            background: #F6F6F6;
            box-shadow: none;
            cursor: default;
            color: #353535;
        }
    }
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

    &.active-calendar {
        background: #FBCA12;
        box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.08), inset 0px -1.5px 0px rgba(0, 0, 0, 0.08);
        border-radius: 4px;
    }
}

.select-btn {
    border-left: 1px solid #E2E2E2;
    height: 100%;
    display: flex;
    justify-content: center;

    @media (max-width: 1200px) {
        border: none;
        border-top: 1px solid #E2E2E2;
        height: 63px;
        width: 288px;
        justify-content: flex-start;
        padding: 0px 16px;
    }
    
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
        background: url('../assets/Chevron-bottom.svg') 89% no-repeat #FFFFFF;
        padding-left: 20px;

        @media (max-width: 1200px) {
            padding-left: 0px;
            width: 100%;
            background: url('../assets/Chevron-bottom.svg') 95% no-repeat #FFFFFF;
        }
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

        @media (max-width: 1200px) {
            width: 288px;
        }
    }
    img {
        padding-left: 10px;
    }
}
</style>