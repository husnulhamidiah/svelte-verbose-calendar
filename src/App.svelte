<script>
  import { afterUpdate } from 'svelte'
  import { writable } from 'svelte/store'
  import { flip } from 'svelte/animate'
  import { fade } from 'svelte/transition'
  import tippy from 'tippy.js'

  const now = new Date()
  const year = writable(now.getFullYear())
  const generateMonths = (year) => {
    return [...Array(12).keys()]
      .map(month => {
        now.setMonth(month)
        return now.toLocaleString(undefined, { month: 'long' })
      })
      .map((month, index) => {
        return {
          name: month,
          days: new Date(year, index + 1, 0).getDate()
        }
      })
  }

  $: data = {
    year: $year, 
    months: generateMonths($year)
  }

  const previousYearHandle = () => year.update(y => y - 1)
  const nextYearHandle = () => year.update(y => y + 1)
  const dateHandle = (event) => console.log(event.target.getAttribute("data-day"))

  afterUpdate(() => tippy('[data-tippy-content]'))
</script>

<main>
  <div class="flex flex-col">
    <div class="flex flex-row">
      <div class="flex flex-row">
        {#each data.year.toString().split('') as char, index (index)}
          <div class="text-white text-5xl w-[100px] h-[100px] bg-black border border-slate-200 border-solid flex justify-center items-center" animate:flip="{{delay: 250, duration: 300}}">{ char }</div>
        {/each}
      </div>
      <div class="flex flex-col">
        <div class="text-white w-[50px] h-[50px] bg-black border border-slate-200 border-solid flex justify-center items-center hover:bg-sky-600" on:click={nextYearHandle} on:keyup={nextYearHandle}>→</div>
        <div class="text-white w-[50px] h-[50px] bg-black border border-slate-200 border-solid flex justify-center items-center hover:bg-sky-600" on:click={previousYearHandle} on:keyup={previousYearHandle}>←</div>
      </div>
    </div>

    {#key data}
      {#each data.months as month, i}
        <div in:fade="{{delay: (250 + i * 100), duration: 100}}" out:fade="{{duration: 50}}" class="flex flex-row">
          {#each month.name.split('') as char}
            <div class="text-white uppercase w-[50px] h-[50px] bg-black border border-slate-200 border-solid flex justify-center items-center">{ char }</div>
          {/each}
        </div>
        <div in:fade="{{delay: (250 + i * 100), duration: 100}}" out:fade="{{duration: 50}}" class="flex flex-row flex-wrap">
          {#each {length: month.days} as _, j}
            <div class="w-[50px] h-[50px] border border-slate-200 border-solid flex justify-center items-center hover:bg-stone-300" class:bg-cyan-200="{ data.year == now.getFullYear() && i == now.getMonth() - 1 && j == now.getDate() - 1 }" data-tippy-content="{ new Date(data.year, i, j + 1).toLocaleString(undefined, {weekday: 'long'}) }" on:click={dateHandle} on:keyup={dateHandle} data-day="{ j + 1 }">{ j + 1 }</div>
          {/each}
        </div>
      {/each}
    {/key}
  </div>
</main>
