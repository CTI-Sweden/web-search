<script lang="ts">
  import * as Pagination from "$lib/components/ui/pagination";


  import SearchBar from "$lib/components/SearchBar.svelte";
  import SearchResults from "$lib/components/SearchResults.svelte"
</script>

<div>
  <div class="ml-auto mr-auto w-fit">
    <h1 class="text-3xl m-4">Search</h1>
  </div>

  <div class="w-1/4 ml-auto mr-auto">
    <SearchBar />
  </div>

  <SearchResults />

</div>
