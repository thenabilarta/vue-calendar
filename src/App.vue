<template>
  <div id="app">
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
        INITIAL_YEAR: 2020,
        INITIAL_MONTH: 11,
        weekdays: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
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
            this.INITIAL_YEAR,
            this.INITIAL_MONTH
          ),
          ...this.createDaysForCurrentMonth(
            this.INITIAL_YEAR,
            this.INITIAL_MONTH
          ),
          ...this.createDaysForNextMonth(this.INITIAL_YEAR, this.INITIAL_MONTH),
        ];
      },
    },
  };
</script>
