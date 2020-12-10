<template>
  <div id="app">
    <p>Welcome to Vue JS</p>
    <p>{{ getDays }}</p>
    <ol>
      <li v-for="(w, index) in weekdays" :key="index">{{ w }}</li>
    </ol>
  </div>
</template>

<script>
  import dayjs from 'dayjs';
  import weekday from 'dayjs/plugin/weekday';
  import weekOfYear from 'dayjs/plugin/weekOfYear';

  dayjs.extend(weekday);
  dayjs.extend(weekOfYear);

  console.log(dayjs().get('year'));

  const monthsInYear = [
    'Jan',
    'Feb',
    'March',
    'Apr',
    'May',
    'June',
    'July',
    'Aug',
    'Sep',
    'Oct',
    'Nov',
    'Dec',
  ];

  let currentMonthDays;

  const WEEKDAYS = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
  const TODAY = dayjs().format('YYYY-MM-DD');

  const INITIAL_YEAR = dayjs().format('YYYY');

  console.log('Initial Year = ' + INITIAL_YEAR);

  const INITIAL_MONTH = dayjs().format('M');

  console.log('Initial Month = ' + INITIAL_MONTH);

  console.log(dayjs().hour());

  console.log(dayjs(new Date(2020, 4, 5)));

  console.log('Hari apa? ' + dayjs(new Date(2020, 3, 5)).day());

  console.log(dayjs('2020-12-01').subtract(1, 'month'));

  console.log(dayjs(new Date(INITIAL_YEAR, INITIAL_MONTH - 1, 1)).hour());

  function createCalendar(year = INITIAL_YEAR, month = INITIAL_MONTH) {
    currentMonthDays = createDaysForCurrentMonth(
      year,
      month,
      dayjs(`${year}-${month}-01`).daysInMonth()
    );

    console.log(currentMonthDays);

    console.log('getWeekday isinya = ' + currentMonthDays[0].date);
  }

  function getNumberOfDaysInMonth(year, month) {
    console.log(
      'Number of days in Month = ' + dayjs(`${year}-${month}-01`).daysInMonth()
    );
    return dayjs(`${year}-${month}-01`).daysInMonth();
  }

  function createDaysForCurrentMonth(year, month) {
    return [...Array(getNumberOfDaysInMonth(year, month))].map((day, index) => {
      return {
        date: dayjs(`${year}-${month}-${index + 1}`).format('YYYY-MM-DD'),
        dayOfMonth: index + 1,
        isCurrentMonth: true,
      };
    });
  }

  function createDaysForPreviousMonth(year, month) {
    const firstDayOfTheMonthWeekday = getWeekday(currentMonthDays[0].date);

    const previousMonth = dayjs(`${year}-${month}-01`).subtract(1, 'month');

    // Cover first day of the month being sunday (firstDayOfTheMonthWeekday === 0)
    const visibleNumberOfDaysFromPreviousMonth = firstDayOfTheMonthWeekday
      ? firstDayOfTheMonthWeekday - 1
      : 6;

    const previousMonthLastMondayDayOfMonth = dayjs(currentMonthDays[0].date)
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
  }

  function getWeekday(date) {
    console.log('Isi dari getweekday = ' + dayjs(date).weekday());
    return dayjs(date).weekday();
  }

  export default {
    name: 'App',
    data() {
      return {
        weekdays: WEEKDAYS,
      };
    },
    computed: {
      getDays() {
        return monthsInYear[dayjs().get('month')];
      },
    },
    mounted() {
      console.log(monthsInYear[0]);
      console.log(WEEKDAYS);
      console.log(TODAY);
      createCalendar();
      console.log(createDaysForPreviousMonth());
    },
  };
</script>
