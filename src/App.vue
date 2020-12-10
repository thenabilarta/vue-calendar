<template>
  <div id="app">
    <ol class="day-of-week" id="days-of-week">
      <li v-for="(w, index) in weekdays" :key="index">{{ w }}</li>
    </ol>
    <ol class="days-grid" id="calendar-days">
      <li class="calendar-day" v-for="(c, index) in getDays" :key="index">
        {{ c.dayOfMonth }}
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

  [...Array(9)].map((angka, index) => {
    console.log(index);
  });

  let currentMonthDays;

  console.log(dayjs(new Date(2020, 5)).format('MMMM YYYY'));

  const WEEKDAYS = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
  const TODAY = dayjs().format('YYYY-MM-DD');

  // const INITIAL_YEAR = dayjs().format('YYYY');
  const INITIAL_YEAR = 2019;

  console.log('Initial Year = ' + INITIAL_YEAR);

  // const INITIAL_MONTH = dayjs().format('M');
  const INITIAL_MONTH = 2;

  console.log('Initial Month = ' + INITIAL_MONTH);

  console.log(dayjs().hour());

  console.log(dayjs(new Date(2020, 4, 5)));

  console.log('Hari apa? ' + WEEKDAYS[dayjs(new Date(2020, 10, 12)).day()]);

  console.log(dayjs('2020-12-01').subtract(1, 'month'));

  console.log(dayjs(new Date(INITIAL_YEAR, INITIAL_MONTH - 1, 1)).hour());

  function createCalendar(year = INITIAL_YEAR, month = INITIAL_MONTH) {
    currentMonthDays = createDaysForCurrentMonth(
      year,
      month,
      dayjs(`${year}-${month}-01`).daysInMonth()
    );

    console.log('Isi current month days = ' + currentMonthDays);

    console.log('Isi current month days[date] = ' + currentMonthDays[0].date);

    console.log('getWeekday isinya = ' + getWeekday(currentMonthDays[0].date));
  }

  function getNumberOfDaysInMonth(year, month) {
    console.log(
      'Number of days in Month = ' + dayjs(`${year}-${month}-01`).daysInMonth()
    );
    return dayjs(`${year}-${month}-01`).daysInMonth();
  }

  function createDaysForCurrentMonth(
    year = INITIAL_YEAR,
    month = INITIAL_MONTH
  ) {
    return [...Array(getNumberOfDaysInMonth(year, month))].map((day, index) => {
      return {
        date: dayjs(`${year}-${month}-${index + 1}`).format('YYYY-MM-DD'),
        dayOfMonth: index + 1,
        isCurrentMonth: true,
      };
    });
  }

  function createDaysForPreviousMonth(
    year = INITIAL_YEAR,
    month = INITIAL_MONTH
  ) {
    currentMonthDays = createDaysForCurrentMonth(
      year,
      month,
      dayjs(`${year}-${month}-01`).daysInMonth()
    );

    const firstDayOfTheMonthWeekday = getWeekday(currentMonthDays[0].date);

    const previousMonth = dayjs(`${year}-${month}-01`).subtract(1, 'month');

    console.log(
      'First day of the month weekday isinya = ' + firstDayOfTheMonthWeekday
    );

    console.log('Previous month isinya = ' + previousMonth);

    console.log(
      'Previous month diubah dari unix isinya = ' + previousMonth.month()
    );

    // Cover first day of the month being sunday (firstDayOfTheMonthWeekday === 0)
    const visibleNumberOfDaysFromPreviousMonth = firstDayOfTheMonthWeekday
      ? firstDayOfTheMonthWeekday - 1
      : 6;

    console.log(
      'Visible number of days from previous months = ' +
        visibleNumberOfDaysFromPreviousMonth
    );

    console.log(
      'Isi current month days[0].date = ' +
        dayjs(currentMonthDays[0].date)
          .subtract(visibleNumberOfDaysFromPreviousMonth, 'day')
          .date()
    );

    const previousMonthLastMondayDayOfMonth = dayjs(currentMonthDays[0].date)
      .subtract(visibleNumberOfDaysFromPreviousMonth, 'day')
      .date();

    console.log(
      'Isi previous month last monday of the month = ' +
        previousMonthLastMondayDayOfMonth
    );

    console.log([...Array(visibleNumberOfDaysFromPreviousMonth)]);

    return [...Array(visibleNumberOfDaysFromPreviousMonth)].map(
      (day, index) => {
        console.log('Previous month year = ' + previousMonth.year());

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

  function createDaysForNextMonth(year = INITIAL_YEAR, month = INITIAL_MONTH) {
    const lastDayOfTheMonthWeekday = getWeekday(
      `${year}-${month}-${currentMonthDays.length}`
    );

    const nextMonth = dayjs(`${year}-${month}-01`).add(1, 'month');

    const visibleNumberOfDaysFromNextMonth = lastDayOfTheMonthWeekday
      ? 7 - lastDayOfTheMonthWeekday
      : lastDayOfTheMonthWeekday;

    return [...Array(visibleNumberOfDaysFromNextMonth)].map((day, index) => {
      return {
        date: dayjs(
          `${nextMonth.year()}-${nextMonth.month() + 1}-${index + 1}`
        ).format('YYYY-MM-DD'),
        dayOfMonth: index + 1,
        isCurrentMonth: false,
      };
    });
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
        return [
          ...createDaysForPreviousMonth(),
          ...createDaysForCurrentMonth(),
          ...createDaysForNextMonth(),
        ];
      },
    },
    mounted() {
      console.log(monthsInYear[0]);
      console.log(WEEKDAYS);
      console.log(TODAY);
      createCalendar();
      console.log(createDaysForCurrentMonth());
    },
  };
</script>
