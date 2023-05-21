<script>
  import { supabase } from "./db";
  import TodoList from "./TodoList.svelte";
  import { writable } from 'svelte/store';


  const view = writable({
    definitions: []
  });

  export let user;

  let searchWord = "";
  let searchResults = "";
  let definitionList = [];
  let definitionListStr = "";
</script>

<div
  class="w-full h-full flex flex-col justify-center items-center p-4"
  style="min-width: 250px; max-width: 600px; margin: auto;"
>
    <input
    placeholder="Type some gibberish here"
    on:change={(value) => searchWord = value.target.value }
    on:input={(value) => console.log(searchWord) }
    >
  <!-- <form class="form-wrapper cf">
    <input type="text" placeholder="Search here for a word..." on:input={(value) => { console.log(value) } } required>
    <button type="submit">Search</button>
    </form> -->
    <div class="byline">
    <p>search box by <a href="http://speckyboy.com/2012/02/15/how-to-build-a-stylish-css3-search-box/">SpeckyBoy</a> featured on <a href="http://thecodeblock.com/search-box-tutorials-using-css3-jquery/">THE CODE BLOCK</a></p>
  </div>
  <!-- <TodoList {user} /> -->
  <button
    class="btn-black w-full mt-12"
    on:click={async () => {
        console.log("Search word: " + searchWord);
        fetch("https://api.dictionaryapi.dev/api/v2/entries/en/" + searchWord)
        .then(response => response.json())
        .then(data => {
          // console.log(data);
          try {
            if(data[0].meanings.length > 0) {
              for(var i = 0 ; i < data[0].meanings.length; i++) {
                if(data[0].meanings[i].definitions.length > 0) {
                  for(var j = 0; j < data[0].meanings[i].definitions.length; j++) {
                    if(data[0].meanings[i].definitions[j]){
                      $view.definitions.push(data[0].meanings[i].definitions[j].definition);
                    }
                  }
                }
              }
              console.log($view.definitions);
              definitionListStr = JSON.stringify(definitionList);
            }
          } catch(error) {
            console.log(error);
          }
          searchResults = JSON.stringify(data);
        }).catch(error => {
          console.log(error);
          return [];
        });
    }}
  >
    Search Dictionary
  </button>
  <h6>List of definitions: </h6>
  {#if $view.definitions.length > 0}
      <ol>
        {#each $view.definitions as def}
          <li>{def}</li>
        {:else}
          <p>No definitions found...</p>
        {/each} 
      </ol>
  {/if}
</div>
