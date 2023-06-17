<script>
  import { onMount } from "svelte";
  import "./style.scss";

  export let sDate = "";
  export let eDate = "";
  export let single = true;
  export let dateChange = (sDate, eDate) => {};
  export let calendarDate = dateTimeZero(new Date());

  const today = dateTimeZero(new Date());
  const weekDays = ["Pzt", "Sal", "Çar", "Per", "Cum", "Cmt", "Paz"];
  let dayList = [];
  let activeDate = calendarDate;
  let activeMonth = activeDate.getMonth();
  let activeYear = activeDate.getFullYear();
  let newSet = true;

  function dateTimeZero(date) {
    const d = new Date(date);
    return new Date(d.getFullYear(), d.getMonth(), d.getDate(), 0, 0, 0);
  }

  function generateCalendar() {
    dayList = [];
    const numberOfDaysInMonth = new Date(activeYear, activeMonth + 1, 0).getDate();
    const numberOfDaysLastMonth = new Date(activeYear, activeMonth, 0).getDate();
    const firstDayInMonth = new Date(activeYear, activeMonth, 1).getDay() || 7;
    const lastDayInMonth = new Date(activeYear, activeMonth, numberOfDaysInMonth).getDay() || 7;
    for (let i = numberOfDaysLastMonth - firstDayInMonth + 1; i < numberOfDaysLastMonth; i++) {
      const currentDate = dateTimeZero(new Date(activeYear, activeMonth - 1, i));

      const day = {
        key: `disable-${i}`,
        date: currentDate,
        text: i,
        today: false,
        selected: currentDate === dateTimeZero(new Date(sDate)) || currentDate === dateTimeZero(new Date(eDate)),
        range: currentDate > dateTimeZero(new Date(sDate)) && currentDate < dateTimeZero(new Date(eDate)),
        disable: true,
      };

      dayList.push(day);
    }

    for (let i = 1; i <= numberOfDaysInMonth; i++) {
      const currentDate = dateTimeZero(new Date(activeYear, activeMonth, i));
      const day = {
        key: `active-${i}`,
        date: currentDate,
        text: i,
        today: today.getTime() === currentDate.getTime(),
        selected:
          currentDate.getTime() === dateTimeZero(new Date(sDate)).getTime() ||
          currentDate.getTime() === dateTimeZero(new Date(eDate)).getTime(),
        range: currentDate > dateTimeZero(new Date(sDate)) && currentDate < dateTimeZero(new Date(eDate)),
        disable: false,
      };
      dayList.push(day);
    }
    for (let i = 1; i <= 7 - lastDayInMonth; i++) {
      const currentDate = dateTimeZero(new Date(activeYear, activeMonth + 1, i));

      const day = {
        key: `disable-${i}`,
        date: currentDate,
        text: i,
        today: false,
        selected: currentDate === dateTimeZero(new Date(sDate)) || currentDate === dateTimeZero(new Date(eDate)),
        range: currentDate > dateTimeZero(new Date(sDate)) && currentDate < dateTimeZero(new Date(eDate)),
        disable: true,
      };

      dayList.push(day);
    }
    dayList = dayList;
  }

  function navigationBtnHandle(direction) {
    if (direction === "pre") {
      activeDate.setMonth(activeMonth - 1);
    } else if (direction === "next") {
      activeDate.setMonth(activeMonth + 1);
    }
    activeDate = activeDate;
    activeMonth = activeDate.getMonth();
    activeYear = activeDate.getFullYear();
    generateCalendar();
  }

  function dayClassNames(today, selected, range, disable) {
    let classNames = ["day"];
    if (today) classNames.push("today");
    if (selected) classNames.push("selected");
    if (range) classNames.push("range");
    if (disable) classNames.push("disable");

    return classNames.join(" ");
  }

  function changeDate({ date }) {
    if (!single) {
      if (sDate > date && !newSet) {
        eDate = sDate;
        sDate = date;
        newSet = true;
      } else if (newSet) {
        sDate = date;
        eDate = date;
        newSet = false;
      } else if (!newSet) {
        eDate = date;
        newSet = true;
      }
    } else {
      sDate = date;
      eDate = date;
    }
    generateCalendar();
    dateChange(sDate, eDate);
  }

  onMount(() => {
    generateCalendar();
  });
</script>

<div class="calendar">
  <div class="navigation">
    <button class="navigation-btn" on:click={() => navigationBtnHandle("pre")}>
      <ion-icon name="chevron-back-outline" />
    </button>
    <span class="date">{activeDate.toLocaleDateString("tr-TR", { month: "long", year: "numeric" })}</span>
    <button class="navigation-btn" on:click={() => navigationBtnHandle("next")}>
      <ion-icon name="chevron-forward-outline" />
    </button>
  </div>
  <div class="week-days">
    {#each weekDays as day}
      <span> {day} </span>
    {/each}
  </div>
  <div class="month-days">
    {#each dayList as day}
      <span class={dayClassNames(day.today, day.selected, day.range, day.disable)} on:click={() => changeDate(day)}
        >{day.text}</span
      >
    {/each}
  </div>
</div>
