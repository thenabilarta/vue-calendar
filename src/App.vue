<template>
  <div id="app">
    <header>
      <sui-dropdown
        placeholder="Gender"
        selection
        :options="year"
        v-model="INITIAL_YEAR.value"
      />
      <sui-dropdown
        placeholder="Gender"
        selection
        :options="month"
        v-model="INITIAL_MONTH.value"
      />
    </header>
    <ol class="day-of-week" id="days-of-week">
      <li class="day-list" v-for="(w, index) in weekdays" :key="index">
        {{ w }}
      </li>
    </ol>
    <ol class="days-grid" id="calendar-days">
      <li
        class="calendar-day"
        :class="c.isCurrentMonth ? '' : 'not-current'"
        v-for="(c, index) in calendarList"
        :key="index"
      >
        <span>{{ c.dayOfMonth }}</span>
      </li>
    </ol>
  </div>
</template>

<script>
  import dayjs from 'dayjs';
  import weekday from 'dayjs/plugin/weekday';
  import weekOfYear from 'dayjs/plugin/weekOfYear';

  import './style.css';

  dayjs.extend(weekday);
  dayjs.extend(weekOfYear);

  export default {
    name: 'App',
    data() {
      return {
        INITIAL_YEAR: { text: '2020', value: 2020 },
        INITIAL_MONTH: { text: 'December', value: 12 },
        weekdays: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        current: null,
        year: [
          {
            text: '2015',
            value: 2015,
          },
          {
            text: '2016',
            value: 2016,
          },
          {
            text: '2017',
            value: 2017,
          },
          {
            text: '2018',
            value: 2018,
          },
          {
            text: '2019',
            value: 2019,
          },
          {
            text: '2020',
            value: 2020,
          },
          {
            text: '2021',
            value: 2021,
          },
          {
            text: '2022',
            value: 2022,
          },
          {
            text: '2023',
            value: 2023,
          },
          {
            text: '2024',
            value: 2024,
          },
          {
            text: '2025',
            value: 2025,
          },
        ],
        month: [
          {
            text: 'January',
            value: 1,
          },
          {
            text: 'February',
            value: 2,
          },
          {
            text: 'March',
            value: 3,
          },
          {
            text: 'April',
            value: 4,
          },
          {
            text: 'May',
            value: 5,
          },
          {
            text: 'June',
            value: 6,
          },
          {
            text: 'July',
            value: 7,
          },
          {
            text: 'August',
            value: 8,
          },
          {
            text: 'September',
            value: 9,
          },
          {
            text: 'October',
            value: 10,
          },
          {
            text: 'November',
            value: 11,
          },
          {
            text: 'December',
            value: 12,
          },
        ],
      };
    },
    methods: {
      getWeekday(date) {
        return dayjs(date).weekday();
      },
      createDaysForCurrentMonth(year, month) {
        return [...Array(this.getNumberOfDaysInMonth(year, month))].map(
          (day, index) => {
            return {
              date: dayjs(`${year}-${month}-${index + 1}`).format('YYYY-MM-DD'),
              dayOfMonth: index + 1,
              isCurrentMonth: true,
            };
          }
        );
      },
      createDaysForPreviousMonth(year, month) {
        let currentMonthDays = this.createDaysForCurrentMonth(
          year,
          month,
          dayjs(`${year}-${month}-01`).daysInMonth()
        );

        const firstDayOfTheMonthWeekday = this.getWeekday(
          currentMonthDays[0].date
        );

        const previousMonth = dayjs(`${year}-${month}-01`).subtract(1, 'month');

        // Cover first day of the month being sunday (firstDayOfTheMonthWeekday === 0)
        const visibleNumberOfDaysFromPreviousMonth = firstDayOfTheMonthWeekday
          ? firstDayOfTheMonthWeekday - 1
          : 6;

        const previousMonthLastMondayDayOfMonth = dayjs(
          currentMonthDays[0].date
        )
          .subtract(visibleNumberOfDaysFromPreviousMonth, 'day')
          .date();

        return [...Array(visibleNumberOfDaysFromPreviousMonth)].map(
          (day, index) => {
            return {
              date: dayjs(
                `${previousMonth.year()}-${previousMonth.month() +
                  1}-${previousMonthLastMondayDayOfMonth + index}`
              ).format('YYYY-MM-DD'),
              dayOfMonth: previousMonthLastMondayDayOfMonth + index,
              isCurrentMonth: false,
            };
          }
        );
      },
      createDaysForNextMonth(year, month) {
        let currentMonthDays = this.createDaysForCurrentMonth(
          year,
          month,
          dayjs(`${year}-${month}-01`).daysInMonth()
        );

        const lastDayOfTheMonthWeekday = this.getWeekday(
          `${year}-${month}-${currentMonthDays.length}`
        );

        const nextMonth = dayjs(`${year}-${month}-01`).add(1, 'month');

        const visibleNumberOfDaysFromNextMonth = lastDayOfTheMonthWeekday
          ? 7 - lastDayOfTheMonthWeekday
          : lastDayOfTheMonthWeekday;

        return [...Array(visibleNumberOfDaysFromNextMonth)].map(
          (day, index) => {
            return {
              date: dayjs(
                `${nextMonth.year()}-${nextMonth.month() + 1}-${index + 1}`
              ).format('YYYY-MM-DD'),
              dayOfMonth: index + 1,
              isCurrentMonth: false,
            };
          }
        );
      },
      getNumberOfDaysInMonth(year, month) {
        return dayjs(`${year}-${month}-01`).daysInMonth();
      },
    },
    computed: {
      calendarList() {
        return [
          ...this.createDaysForPreviousMonth(
            this.INITIAL_YEAR.value,
            this.INITIAL_MONTH.value
          ),
          ...this.createDaysForCurrentMonth(
            this.INITIAL_YEAR.value,
            this.INITIAL_MONTH.value
          ),
          ...this.createDaysForNextMonth(
            this.INITIAL_YEAR.value,
            this.INITIAL_MONTH.value
          ),
        ];
      },
    },
  };
</script>
