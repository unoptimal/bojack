<script>
  import YoutubePlayer from './YoutubePlayer.svelte';
  import BojackTimeline from './BojackTimeline.svelte';
  import BarChart from './BarChart.svelte';
  import data from './data/bojackData.json';
  import { onMount } from 'svelte';

  let start;
  let autoplay = false;
  function handleStartSelected(e) {
    start = e.detail;
    autoplay = true;

  }
  $: addEventListener('startSelected', handleStartSelected);

  let windowWidth = window.innerWidth;

function updateWindowWidth() {
  windowWidth = window.innerWidth;
}

onMount(() => {
  window.addEventListener("resize", updateWindowWidth);
});

</script>

<h1>I tracked every running gag in Bojack Horseman</h1>
<h2>by <a href="https://unoptimal.com/">unoptimal</a></h2>
<p style="display:flex; justify-content:center; font-size: 20px;">Best viewed on desktop</p>

<YoutubePlayer start={start} autoplay={autoplay}/>
<BojackTimeline {data} {start} /> 

<p><i>Warning: Minor spoilers, obviously.</i></p>
<p>I recently rewatched <i>Bojack Horseman</i> and got curious about the frequencies of the recurring gags, so I mapped them onto an interactive timeline.</p>
<p>For those wondering about each gag's total appearences, here you go:</p>
<BarChart/>
<p style='margin-bottom: 0px'>I also ended up cutting out a few gags because of their limited appearances:</p>
<div style="display: flex; align-items: center; justify-content: center;font-size:18px;">
  <ul style="text-align: left;">
      <li><a href="https://www.youtube.com/watch?v=iMV8btPW4wU">Vincent Adultman</a> (S1-S2)</li>
      <li><a href="https://www.youtube.com/watch?v=3XLVGdN8-wo">What are you doing here?</a> (S2)</li>
      <li><a href="https://www.youtube.com/watch?v=ZB4fiFZtkx0">That's too much, man!</a> (S1-S3)</li>
      <li>All of <a href="https://bojackhorseman.fandom.com/wiki/Paige_Sinclair">Paige Sinclair's </a>lines (S6)</li>
      <li><a href='https://www.youtube.com/watch?v=wlfK51tN-xo'>Generic Decade Songs</a> (3 appearances)</li>
      <li><a href="https://www.youtube.com/watch?v=BbjnQ-D_fEQ">Halloween in January</a> (3 appearances)</li>
        <ul>
        <li>This gag contains one of my <a href="https://www.youtube.com/watch?v=g0ajIJB_z0k">favorite scenes</a> from the show.</li>
        </ul>
      <li><a href='https://bojackhorseman.fandom.com/wiki/A_Ryan_Seacrest_Type'>A Ryan Seacrest Type</a> (4 appearances)</li>
    </ul>
</div>
<p style='margin-top:0px;'>You can view the source code for this project <a href="https://github.com/unoptimal/bojack">here</a>, and comments <a href='https://www.reddit.com/r/BoJackHorseman/comments/10z9hmt/oc_i_tracked_every_running_gag_in_bojack_horseman/'>here</a>.</p>

<style>
  h1{
    text-align: center;
    font-size: 50px;
    margin-bottom: 0px;
  }

  a{
    text-decoration: none;
  }

  h2{
    text-align: center;
    font-size: 25px;
  }

  p{
    text-align: left;
    font-size: 18px;
    margin: 20px auto;
    width: 50%;
}


  @media only screen and (max-width: 767px) {
  h1 {
    text-align: center;
    font-size: 30px;
  }
  h2 {
    text-align: center;
    font-size: 20px;
  }
  p {
    text-align: left;
    font-size: 18px;
    margin: 20px auto;
    width: 75%;
  }
}
  @media only screen and (min-width: 1700px) {
  h1 {
    font-size: 60px;
  }
  h2 {
    font-size: 30px;
  }
  p{
    font-size: 25px;
  }
}


</style>
