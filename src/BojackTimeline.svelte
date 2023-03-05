<script>
	import * as d3 from 'd3';
    import { select } from 'd3';
	import { onMount, onDestroy} from 'svelte';
    import { svg_element } from 'svelte/internal';
	
   	export let data, start;

	let windowWidth = window.innerWidth;

	window.addEventListener('resize', () => {
		windowWidth = window.innerWidth;
	});

	let timelineContainerWidth = 1500; 
	let timelineContainerHeight = 100;
	let selectedGag;

	let season = 1; 
	let seasonLengths = [18180, 18720, 18720, 18720, 18720, 25020];

	const colors = ['blue', 'red', 'green', 'orange', 'purple', 'pink', 'brown', 'cyan', 'indigo', 'lime', 'teal'];
	let legendData = [];
	let uniqueGags = [];

	data.gags.forEach(gag => {
	if (!uniqueGags.includes(gag.name)) {
		uniqueGags.push(gag.name);
	}
	});

	uniqueGags.forEach((gag, index) => {
		legendData.push({ name: gag, color: colors[index % colors.length] });
	});

	data.gags.forEach(gag => {
	const match = legendData.find(ld => ld.name === gag.name);
	gag.color = match.color;
	});

	$: mappedData = data.gags
	.filter(gag => gag.season === season)
	.map(d => {
	  let time = d.relative.split(":");
	  let seconds = +time[0] * 60 * 60 + +time[1] * 60 + +time[2];
	  d.position = xScale(seconds);
	  return d;
	});

	$: mappedEpisodeMarkers = data.episodes
	.filter(ep => ep.season === season)
	.map(d => {
	  let time = d.timestamp.split(":");
	  let seconds = +time[0] * 60 * 60 + +time[1] * 60 + +time[2];
	  d.position = xScale(seconds);
	  return d;
	});


$: xScale = d3.scaleLinear()
	   .range([0,  timelineContainerWidth - (timelineContainerWidth * .10)]) 
	   .domain([0, seasonLengths[season - 1]]); 

	onMount(async () => {
		window.requestAnimationFrame(() => {
		timelineContainerWidth = document.getElementById('timeline-container').offsetWidth;
		timelineContainerHeight = document.getElementById('timeline-container').offsetHeight;
		window.addEventListener('resize', () => {
			timelineContainerWidth = document.getElementById('timeline-container').offsetWidth;
		});
	});
});
	
	onDestroy(() => {
		window.removeEventListener('resize', () => {
			timelineContainerWidth = document.getElementById('timeline-container').offsetWidth;
		});
	});


	function handleClick(gag) {
		selectedGag = gag;
		start = gag.start;
		const event = new CustomEvent('startSelected', { detail: start});
   		dispatchEvent(event);
  	}

</script>


{#if windowWidth < 600}

<div id='legend' style='margin-top: 10px;'>
	<ul style="list-style-type: none;">
	  {#each legendData as { name, color }}
		<li style='font-size: 12px; margin-bottom: .5rem;'>
		  <span style="display: inline-block; width: .5rem; height: .5rem; border-radius: 50%; background-color: {color};"></span>
		  {name}
		</li>
	  {/each}
	</ul>
  </div>

{:else if windowWidth < 769}
  
<div id='legend' style='margin-top: 50px;'>
	<ul style="display: flex; justify-content: center; flex-wrap: wrap; list-style-type: none;">
	  {#each legendData.slice(0, 4) as { name, color }}
		<li style='font-size: 18px; margin-right: 1rem; margin-bottom: 0.5rem;'>
		  <span style="display: inline-block; width: 1rem; height: 1rem; border-radius: 50%; background-color: {color};"></span>
		  {name}
		</li>
	  {/each}
	</ul>
	<ul style="display: flex; justify-content: center; flex-wrap: wrap; list-style-type: none;">
		{#each legendData.slice(4, 7) as { name, color }}
		  <li style='font-size: 20px; margin-right: 1rem; margin-bottom: 0.5rem;'>
			<span style="display: inline-block; width: 1rem; height: 1rem; border-radius: 50%; background-color: {color};"></span>
			{name}
		  </li>
		{/each}
	  </ul>
	  <ul style="display: flex; justify-content: center; flex-wrap: wrap; list-style-type: none;">
		{#each legendData.slice(7, 9) as { name, color }}
		  <li style='font-size: 20px; margin-right: 1rem; margin-bottom: 0.5rem;'>
			<span style="display: inline-block; width: 1rem; height: 1rem; border-radius: 50%; background-color: {color};"></span>
			{name}
		  </li>
		{/each}
	  </ul>
	  <ul style="display: flex; justify-content: center; flex-wrap: wrap; list-style-type: none;">
		{#each legendData.slice(9, 12) as { name, color }}
		  <li style='font-size: 20px; margin-right: 1rem; margin-bottom: 0.5rem;'>
			<span style="display: inline-block; width: 1rem; height: 1rem; border-radius: 50%; background-color: {color};"></span>
			{name}
		  </li>
		{/each}
	  </ul>
  </div>

{:else}
<div id='legend' style="margin-top: 50px;">
	<ul style="display: flex; justify-content: center; flex-wrap: wrap; list-style-type: none;">
		{#each legendData as { name, color }}
		<li style='font-size: 20px; margin-right: 1rem; margin-bottom: 0.5rem;'>
			<span style="display: inline-block; width: 1rem; height: 1rem; border-radius: 50%; background-color: {color};"></span>
			{name}
		</li>
		{/each}
	</ul>
</div>
{/if}

<div id="timeline-container" >
	<svg width={timelineContainerWidth} height=100>
		<g transform={`translate(${timelineContainerWidth * 0.05} ${timelineContainerHeight * 0.5})`}>
			<line x1="0" y1="0" x2={timelineContainerWidth - (timelineContainerWidth * .10)} y2="0" stroke="black" stroke-width="5"/>			
			<g>
				<!-- fix the keydown. it does not do a thing lmfao -->
				<!-- also on desktop i want to allow for hover? but not overlap with onclick obviously-->
				{#each mappedData as gag}
				<circle cx={gag.position} cy={0} r={windowWidth > 1023 ? 8 : (windowWidth > 520 ? 7: 4.5)} fill={gag === selectedGag ? gag.color : (gag !== selectedGag && selectedGag ? 'gray' : gag.color)}
						on:click={() => handleClick(gag)}
						on:mouseenter={() => {selectedGag = gag}}
						on:mouseleave={() => {selectedGag = null}}
				/>
				{/each}
				
				
				{#each mappedEpisodeMarkers as ep}
					<line x1={ep.position} y1={-10} x2={ep.position} y2={10} stroke="black" stroke-width="1"/>
					<text x={ep.position} y={40} text-anchor="middle" font-size={windowWidth > 600 ? 20: (windowWidth > 500 ? 15 : 9)} font-weight="bold">E{ep.number}</text>
					<!-- {#if windowWidth < 600}
						<line x1={ep.position} y1={-10} x2={ep.position} y2={10} stroke="black" stroke-width="1"/>
						<text x={ep.position} y={40} text-anchor="middle" font-size={windowWidth > 700 ? 10: (windowWidth > 400 ? 3 : 10)} font-weight="bold">E{ep.number}</text>
					{:else}
						<line x1={ep.position} y1={-10} x2={ep.position} y2={10} stroke="black" stroke-width="3"/>
						<text x={ep.position} y={40} text-anchor="middle" font-size="20px" font-weight="bold">E{ep.number}</text>
					{/if} -->
				{/each}
				
					  
			</g>
		</g>
	</svg>	
</div>

<!-- make this responsive for small size -->
{#if selectedGag}
<div style="display: flex; flex-direction: column; text-align: center; border: 1px solid black; width: 40%; margin: 0 auto;">
	<p style="margin: 15px 15px; ">{selectedGag.name}</p>
	<p>S{selectedGag.season}E{selectedGag.episode}, {selectedGag.timestamp}</p>
</div>
{/if}



<div style="display: flex; justify-content: center; padding: 10px">
	<button style="visibility: {season === 1 ? 'hidden' : 'visible'};" on:click={() => { season = Math.max(1, season - 1); }}> &lt; </button> 
	<h3 style="margin: 0 10px;">Season {season}</h3>
	<button style="visibility: {season === 6 ? 'hidden' : 'visible'};" on:click={() => { season = Math.min(6, season + 1); }}> > </button>
  </div>


