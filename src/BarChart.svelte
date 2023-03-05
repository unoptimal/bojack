<script>
	import * as d3 from 'd3';
  import {onMount, onDestroy} from 'svelte';

  let data = [
    {name: "Voicemails", count: 5},
    {name: "Honeydew", count: 6},
    {name: "Diane's Ringtones", count: 6},
    {name: "The Horse from Horsin’ Around", count: 6},
    {name: "Randy ", count: 7},
    {name: " Mr. Peanutbutter's Catchphrases", count: 8},
    {name: "Character Actress Margo Martindale", count: 9},
    {name: "Sign Company", count: 12},
    {name: "Erica", count: 14},
    {name: "Tongue Twisters", count: 26},
  ];
    
  let barChartWidth = 0; 
	let barChartHeight = 400;

  let windowWidth = window.innerWidth;
  window.addEventListener('resize', () => {
		windowWidth = window.innerWidth;
	});

  $: yScale = d3.scaleBand()
    .domain(data.map((d) => d["name"]))
    .range([barChartHeight, 0])
    .padding(0.2)

  $: xScale = d3.scaleLinear()
   .domain([0, d3.max(data, (d) => d.count)])
   .range([0, barChartWidth / 3]);

  onMount(async () => {
    window.requestAnimationFrame(() => {
      barChartWidth = document.getElementById('bar-container').offsetWidth;
      barChartHeight = document.getElementById('bar-container').offsetHeight;

      window.addEventListener('resize', () => {
        barChartWidth = document.getElementById('bar-container').offsetWidth;
      });
    });
  });
	
	onDestroy(() => {
		window.removeEventListener('resize', () => {
			barChartWidth = document.getElementById('bar-container').offsetWidth;
		});
	});

</script>
<div id='bar-container' style="display: flex; justify-content:center;">
  <svg width={barChartWidth} height=400>
    <g transform={`translate(${windowWidth > 768 ? barChartWidth*.38 : (windowWidth > 520 ? barChartWidth * 0.45 : barChartWidth * 0.5)})`}>
      {#each data as d, i}
      <text
      x={-10}
      y={yScale(d.name) + yScale.bandwidth() / 2 + 2.5}
      text-anchor="end"
      fill="black"
      font-size= {windowWidth > 768 ? 16 : (windowWidth > 374 ? 10 : 9)}
      >
      {d.name}
    </text>    
    
      <rect
        x={0}
        y={yScale(d.name)}
        width={xScale(d.count)}
        height={yScale.bandwidth()}
        style="fill: #aec7e8; stroke: black; stroke-width: 1"
        />

    <text
      x={xScale(d.count) + 10}
      y={yScale(d.name) + yScale.bandwidth() / 2 + 5}
      fill="black"
      text-anchor="start"
      font-size="16"
      >
    {d.count}
  </text>
    {/each}
  </svg>
</div>


