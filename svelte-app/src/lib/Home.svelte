<script>
  import { supabase } from "./db";
  import TodoList from "./TodoList.svelte";
  import { writable } from 'svelte/store';
  import VirtualScroll from "svelte-virtual-scroll-list";

  const view = writable({
    definitions: []
  });

  export let user;

  let searchWord = "";
  let searchResults = "";
  let definitionList = [];
  let definitionListStr = "";
  let rerenderDom = false;
</script>

<div
  class="w-full h-full flex flex-col justify-center items-center p-4"
  style="min-width: 250px; max-width: 600px; margin: auto;"
>
    {#key rerenderDom}
      <div>
          <input
          placeholder="Enter search word"
          class=""
          on:change={(value) => searchWord = value.target.value }
          on:input={(value) => console.log(searchWord) }
          >
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
                        $view.definitions = [];
                        var counter = 0;
                        for(var j = 0; j < data[0].meanings[i].definitions.length; j++) {
                          if(data[0].meanings[i].definitions[j]){
                            $view.definitions.push({id: ++counter, text: data[0].meanings[i].definitions[j].definition});
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
        <button class="btn-black w-full mt-12" on:click={() => {rerenderDom = !rerenderDom; $view.definitions = [] }}>Refresh</button>
        {#if $view.definitions.length > 0}
        <h3>List of definitions: </h3>
        {/if}
        <VirtualScroll
          data={$view.definitions}
          key="id"
          let:data
        >
              <div slot="header">
                {#if $view.definitions.length > 0}
                  -----------------
                {/if}
              </div>
              <div>
                  {data.id}. {data.text}
              </div>
              <div slot="footer">
                {#if $view.definitions.length > 0}
                  -----------------
                {/if}
              </div>
        </VirtualScroll>
        
        <!-- {#if $view.definitions.length > 0}
            <ol>
              {#each $view.definitions as def}
                <li>{def.text}</li>
              {:else}
                <p>No definitions found...</p>
              {/each} 
            </ol>
        {/if} -->
    </div>
  {/key}
</div>
