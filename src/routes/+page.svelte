<script>
    import { onMount } from 'svelte';
  
    const delay = 1000 / 3;
    let firstCrumb =1;
    let SecondCrumb =2;
    let thirdCrumb =3;

    let animeList = [];
    let inputValue = "";
    let queue = false;

    

    function handleAnimeClick(animeId) {
      localStorage.setItem('selectedAnimeId', animeId);
      window.open('/details', '_blank');
    }
  
    function handleChangePage (page){
      animeList= [];
      if (page =="homepage"){
        firstCrumb = 1;
        SecondCrumb = 2;
        thirdCrumb = 3;
        page=firstCrumb;
      }
      else if (page=="goBack") {
        firstCrumb -=1;
        SecondCrumb -=1;
        thirdCrumb -=1;
        page=firstCrumb;
        console.log(page)
      }
      else if (page=="goForward") {
        firstCrumb +=1;
        SecondCrumb +=1;
        thirdCrumb +=1;
        page=firstCrumb;
        console.log(page)
      }
      else {
        firstCrumb = page;
        SecondCrumb = page +1;
        thirdCrumb = page +2;
      }
  
      setTimeout(async () => {
        try {
			const response = await fetch(`https://api.jikan.moe/v4/anime?order_by=popularity&page=${page}`);
  
          if (!response.ok) {
            return;
          }
          const data = await response.json();
		  console.log(data)
		  for (let index = 0; index < 25; index++) {
        if (data.data[index].popularity!=0){
                let title;
              if (data.data[index].title_english==null) {
                title = data.data[index].title;
              }
              else {
                title = data.data[index].title_english;
              }
              const anime = {
            	id:data.data[index].mal_id,
            	imageUrl: data.data[index].images.jpg.image_url,
            	title: title,
          	};
			  animeList = [...animeList, anime];
			
		  }
    }    
  
        } catch (error) {
          console.error(`Error fetching anime ${i}:`, error);
        }
      });
    }
  
  
  onMount(() => {
    for (let i = 0; i < 1; i++) {
      setTimeout(async () => {
        try {
			const response = await fetch(`https://api.jikan.moe/v4/anime?order_by=popularity`);
  
          if (!response.ok) {
            return;
          }
          const data = await response.json();
		  console.log(data)
		  for (let index = 0; index < 25; index++) {
        if (data.data[index].popularity!=0){
                let title;
              if (data.data[index].title_english==null) {
                title = data.data[index].title;
              }
              else {
                title = data.data[index].title_english;
              }
              const anime = {
            	id:data.data[index].mal_id,
            	imageUrl: data.data[index].images.jpg.image_url,
            	title: title,
          	};
				
			  animeList = [...animeList, anime];
			
		  }
    }     
  
        } catch (error) {
          console.error(`Error fetching anime ${i}:`, error);
        }
      }, i * delay);
    }
  });

  async function cerca() {
    animeList = [];
    const breadcrumbElement = document.querySelector(".breadcrumb.flex.justify-center.mb-4");
    breadcrumbElement.style.opacity = 0;
    
    if (inputValue === "") {
      handleChangePage(1);
      const breadcrumbElement = document.querySelector(".breadcrumb.flex.justify-center.mb-4");
      breadcrumbElement.style.opacity = 1;
    }
else{
    if (!queue){
      try {
        const response = await fetch(`https://api.jikan.moe/v4/anime?q=${inputValue}&sfw`);
  
        if (!response.ok) {
          throw new Error(`Errore di rete: ${response.status}`);
        }

        const data = await response.json();
        console.log(data)
        console.log(data.data.length)
        if (data.data.length>0){
        for (let index = 0; index < 25; index++) {
          let title;
          let censura=false;
          if (data.data[index].rating == "Rx - Hentai") censura= true;
            if (!censura){
              if (data.data[index].title_english==null) {
                title = data.data[index].title;
              }
              else {
                title= data.data[index].title_english;
              }
              const anime = {
              id: data.data[index].mal_id,
              imageUrl: data.data[index].images.jpg.image_url,
              title: title,
            };
            animeList = [...animeList, anime];
            }
              
          }
        }
      } catch (error) {
        console.error('Errore durante la richiesta:', error);
          if (animeList.length==0){
            queue = true;
            setTimeout(() => {
            queue = false;
            cerca();
        }, 1000);
          }
      }
    }

}

}

async function random() {
    try {
        const response = await fetch(`https://api.jikan.moe/v4/random/anime`);
        if (!response.ok) {
          throw new Error(`Errore di rete: ${response.status}`);
        }

        const data = await response.json();
        handleAnimeClick(data.data.mal_id)
      } catch (error) {
        console.error('Errore durante la richiesta:', error);
      }
  }
    
  
  
  </script>

<div>
	<img class="h-auto max-w-full mx-auto" src="https://www.animeunity.to/images/Sfondo2.png" alt="">
</div>



<div class="container flex mx-auto ">
<blockquote class="my-4 border-indigo-600 justify-center ml-40 my-8  ">
    <p style="margin-left: -100px;" class="font-sans text-6xl font-medium "><strong>Trending Anime</strong></p>
</blockquote>



<button id="random"
  on:click={random}>Random</button>



<p class="font-sans text-4xl font-medium my-16 ml-40 justify-center"><strong>Search: </strong></p>
<input class="input max-h-12 ml-4 max-w-56 my-16 justify-center" type="search" name="demo" placeholder="Search..."  bind:value={inputValue} on:input={cerca} />
</div>

  
<div class="container flex flex-wrap justify-center items-center mx-auto gap-x-4 gap-y-4 mb-8 ">
	{#each animeList as { id, imageUrl, title}}
  <div class="box">

 
	<div class="card h-[432px] w-64">
		<header class="card-header  "><img class="max-h-[320px]" src="{imageUrl}"  on:click={() => handleAnimeClick(id)} alt=""></header>
		<section class="p-4 text-center font-bold max-h-[56px] ">{title}</section>
		<footer class="card-footer text-center font-semibold">streaming now!</footer>
	</div>
</div>
	{/each}
</div>
<div  class="">
	<ol class="breadcrumb flex justify-center mb-4 mt-4">
    {#if firstCrumb>1}
    <li class="crumb"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage('homepage')} >&lt;&lt;</button></li>

		<li class="crumb go-back"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage('goBack')} >&lt;</button></li>
    {/if}
		<li class="crumb"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage(firstCrumb)} >{firstCrumb}</button></li>
    <li class="crumb"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage(SecondCrumb)} >{SecondCrumb}</button></li>
		<li class="crumb"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage(thirdCrumb)} >{thirdCrumb}</button></li>
    <li class="crumb"><button class="btn btn-sm bg-indigo-600 bg-opacity-75 text-white text-opacity-75" on:click={() => handleChangePage('goForward')} >&gt;</button></li>

  </ol>  
</div>
