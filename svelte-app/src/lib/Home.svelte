<script>
  import { supabase } from "./db";
  import TodoList from "./TodoList.svelte";

  export let user;
</script>

<div
  class="w-full h-full flex flex-col justify-center items-center p-4"
  style="min-width: 250px; max-width: 600px; margin: auto;"
>
  <form class="form-wrapper cf">
    <input type="text" placeholder="Search here for a word..." required>
    <button type="submit">Search</button>
    </form>
    <div class="byline">
    <p>search box by <a href="http://speckyboy.com/2012/02/15/how-to-build-a-stylish-css3-search-box/">SpeckyBoy</a> featured on <a href="http://thecodeblock.com/search-box-tutorials-using-css3-jquery/">THE CODE BLOCK</a></p>
  </div>
  <TodoList {user} />
  <button
    class="btn-black w-full mt-12"
    on:click={async () => {
      const { error } = await supabase.auth.signOut();
      if (error) console.log("Error logging out:", error.message);
    }}
  >
    Logout
  </button>
</div>
