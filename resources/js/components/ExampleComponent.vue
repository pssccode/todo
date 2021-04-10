<template>
<div>
    <div class="container-fluid" style="height: 100vh;">
        <div class="row" style="height: 100%;">
            <div class="col-md-3" style="height: 100%;">
                <div class="left-menu"  style="height: 100%;">
                    <div class="" style="text-align: center;">
                        <h3>
                            <span @click="changeYear(-1)"> < </span>
                                {{ currentYear() }}
                            <span @click="changeYear(1)"> > </span>
                        </h3>

                    </div>

                    <div class="">
                        <ul>
                            <li v-for="month in monthsArr" :class="month.className">{{ month.name }}</li>
                        </ul>
                    </div>
                </div>
            </div>
            <div class="col-md-9">
                <div>
                    <div class="weeks" v-for="w in currentMonthDays">
                        <div class="days" v-for="d in w">
                            <span :class="d.className">{{ d.dayNumber }}</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<style>
    .left-menu{
        background-color: #7385E6;
        color: #fff;
    }
    .weeks{
        padding: 10px;
    }
    .days{
        display: inline;
        border: 1px solid #ccc;
        padding: 10px;
        margin: 10px;
    }
    .prev{
        color: grey;
    }
    .current-day,
    .current-month{
        color: red;
        font-weight: bold;
    }
</style>

<script>
    require('moment/locale/ru');
    export default {
        data() {
            let self = this;
            return {
                currentDate : moment(),
                monthsArr: [],
                currentMonthDays: []
            }
        },
        methods: {
            currentYear()
            {
                return this.currentDate.format('YYYY');
            },
            currentMonth(){
                return this.currentDate.format('MM');
            },
            currentMonthNumber()
            {
                return this.currentDate.format('MM');
            },
            currentMonthName()
            {
                return this.currentDate.format('MMMM');
            },
            prevMonth() {
                return this.currentDate.clone().subtract(1, 'month').format('MMMM');
            },
            nextMonth() {
                return this.currentDate.clone().add(1, 'month').format('MMMM');
            },
            changeMonth(n){
                if(n > 0){
                    this.currentDate.add(1, 'month');
                    this.showMonth(this.currentYear(), this.currentMonthNumber());
                }else{
                    this.currentDate.subtract(1, 'month');
                    this.showMonth(this.currentYear(), this.currentMonthNumber());
                }
            },
            changeYear(n){
                if(n > 0){
                    this.currentDate.add(1, 'year');
                    this.showMonth(this.currentYear(), this.currentMonthNumber());
                }else{
                    this.currentDate.subtract(1, 'year');
                    this.showMonth(this.currentYear(), this.currentMonthNumber());
                }
            },
            showMonth(year, monthNumber) {
                this.currentMonthDays = [];
                let start = moment().year(year).month(monthNumber - 1).date(1),
                    tmp = start.clone().startOf('week'),
                    currentMonth = start.format('M'),
                    currentWeek = start.format('w'),
                    arrIndex = 0;
                this.currentMonthDays[0] = [];

                if(start.weekday() > 0){
                    while (tmp.weekday() != start.weekday()) {
                        this.currentMonthDays[0].push({
                            dayNumber: tmp.format('DD'),
                            className: 'prev'
                        });
                        tmp.add(1, 'days');
                    }
                }

                while (start.format('M') == currentMonth) {
                    if (currentWeek !== start.format('w')) {
                        ++arrIndex;
                    }
                    if (!this.currentMonthDays[arrIndex]) {
                        this.currentMonthDays[arrIndex] = [];
                    }

                    this.currentMonthDays[arrIndex].push({
                        dayNumber: start.format('DD'),
                        className: start.isSame(new Date(), "day") ? 'current-day' : 'normal'
                    });
                    currentWeek = start.format('w');
                    start.add(1, 'days');
                }
                if(start.weekday() > 0 && start.weekday() < 7){
                    tmp = start.clone().endOf('week');
                    while (start.dayOfYear() <= tmp.dayOfYear()) {
                        this.currentMonthDays[arrIndex].push({
                            dayNumber: start.format('DD'),
                            className: 'prev'
                        });
                        start.add(1, 'days');
                    }
                }

                this.createMonthsArr();
                this.$forceUpdate();
            },
            createMonthsArr(){
                this.monthsArr = [];
                let st = this.currentDate.clone().startOf('year'),
                    f = this.currentDate.clone().endOf('year'),
                    currentMonthNumber = moment().format('MM'),
                    currentYear = moment().format('YYYY')

                while (st.isSameOrBefore(f)){
                    console.log(st.format('M'), this.currentMonthNumber(), st.format('YYYY'), this.currentYear());
                    this.monthsArr.push({
                        name: st.format('MMMM'),
                        className: st.format('MM') == currentMonthNumber && st.format('YYYY') == currentYear ? 'current-month' : ''
                    });
                    st.add(1, 'month');
                }
            }
        },
        mounted() {
            this.createMonthsArr();
            this.showMonth(this.currentYear(), this.currentMonthNumber());
        }
    }
</script>
