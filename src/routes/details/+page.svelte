<script>
  import { onMount } from 'svelte';

  export let animeData;

  let animeId;

  onMount(() => {
    animeId = localStorage.getItem('selectedAnimeId');

    if (!animeId) {
      console.error('L\'animeId non è presente in localStorage o è vuoto.');
      return;
    }

    fetchAnimeData();
  });

  async function fetchAnimeData() {
    try {
      const response = await fetch(`https://api.jikan.moe/v4/anime/${animeId}/full`);
      const data = await response.json();
      animeData = data;
    } catch (error) {
      console.error('Errore durante la richiesta dei dati dell\'anime:', error);
    }
  }

</script>

{#if animeData}
  <div class="container mx-auto card">

    <div class="title">
      <h1 class="text-5xl"><strong>{animeData.data.title}<strong></h1>
    </div>
    <br><br>

    <div class="wrapper">
      <div class="poster">
        <img class="h-auto max-w-full rounded-lg" src="{animeData.data.images.jpg.image_url}"> 

          {#if animeData.data.streaming && animeData.data.streaming[0] && animeData.data.streaming[0].url}
            <div class="overlay">
              <h1><a href="{animeData.data.streaming[0].url}"><strong>Stream now!</strong></a></h1>
            </div>
          {/if}
      </div>
      <hr
        class="my-12 h-px border-t-0 bg-transparent bg-gradient-to-r from-transparent via-neutral-500 to-transparent opacity-25 dark:opacity-100" />

      <div class="details mx-auto text-2xl">

        <h1><strong>Japanese title: </strong> {animeData.data.title_japanese}</h1>
        <h1><strong>Length: </strong> {animeData.data.duration}</h1>
        <h1><strong>Episodes: </strong> {animeData.data.episodes}</h1>
        <h1><strong>Popularity: </strong> {animeData.data.popularity}</h1>

        {#if animeData.data.rank}
          <h1><strong>Rank: </strong> {animeData.data.rank}</h1>
          {/if}

        {#if animeData.data.year}
          <h1><strong>Year: </strong> {animeData.data.year}</h1>
          {/if}
      </div>
    </div>


    <hr
      class="my-12 h-px border-t-0 bg-transparent bg-gradient-to-r from-transparent via-neutral-500 to-transparent opacity-25 dark:opacity-100" />
    <div class="card tcontainer mx-auto">
      <div class="trailer mx-auto">
        <h1 class="text-4xl"><strong>Trailer</strong></h1>
        <br>
        <iframe src="{animeData.data.trailer.embed_url}" frameborder="0" width="640" height="360" allowfullscreen></iframe>
      </div>
      <div class="syn mx-auto" width="50">
        <h1 class="text-4xl"><strong>Synopsis</strong></h1>
        <br>

        {#if animeData.data.synopsis}
        <p>{animeData.data.synopsis}</p>
        {:else}
            <p>Synopsis not available.</p>
        {/if}


      </div>
    </div>
  </div>

{/if}

