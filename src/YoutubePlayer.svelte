<script>
    export let start, autoplay;
    $: src = `https://www.youtube.com/embed/qeDGjrbWSOw?start=${start}&autoplay=${autoplay ? 1 : 0}`;
</script>

<iframe id="video"
    width="50%"
    height="400"
    src={src}
    frameborder="0"
    allowfullscreen
    allow="autoplay; clipboard-write; encrypted-media; picture-in-picture; web-share"
    title="Every running gag from Bojack Horseman"
></iframe>
<style>
  #video {
    display: block;
    margin: 0 auto;
  }

  @media only screen and (max-width: 670px) {
    #video {
      width: 80%;
      height: 200px;
    }
  }

  @media only screen and (min-width: 670px) {
    #video {
      width: 60%;
      height: 300px;
    }
  }

  @media only screen and (min-width: 1200px) {
    #video {
      width: 40%;
      height: 300px;
    }
  }

  @media
 only screen and (min-width: 1700px) {
  #video {
    width: 40%;
    height: 500px;
  }
}
</style>