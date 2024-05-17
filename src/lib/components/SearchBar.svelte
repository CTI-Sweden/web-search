<script lang="ts">
  import { Input } from "$lib/components/ui/input";
  import { Button } from "$lib/components/ui/button";


  import axios from 'axios';
  import { searchResultsStore } from "/src/stores/searchResultsStore.ts";

  let searchText = "";
  let timeout;

  function keyPressed() {
    if (timeout) clearTimeout(timeout);
    timeout = setTimeout(() => {
        fetchSearchResults();
      }, 600)
  }

  async function fetchSearchResults() {
    try {
      let {data, status} = await axios.post('http://127.0.0.1:8000/search', {
        text: searchText
      });
      if (status != 200) {
        alert("Error fetching search results!");
        return;
      }
      console.log(data.results)
      searchResultsStore.set(data.results);
    } catch(error) {
      alert("Error fetching search results! Probably the API is not ready yet.");
      return;
    }
  }

</script>

<div class="flex">
  <Input bind:value={searchText}  on:keydown={()=> keyPressed()}  />
  <Button variant="outline">Button</Button>
</div>

