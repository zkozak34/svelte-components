<script>
  import Calendar from "@/lib/Calendar/index.svelte";

  function dateChange(sDate, eDate) {
    console.log(sDate, eDate);
  }
</script>

<main>
  <Calendar single={false} {dateChange} />
</main>

<style>
  main {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
