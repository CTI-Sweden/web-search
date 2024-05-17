<script lang="ts">
  import { ScrollArea } from "$lib/components/ui/scroll-area";

  export let article;
  console.log(article);
</script>
<ScrollArea class="mt-4 ">
  <div class="grid gap-4 py-4">
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="title" class="text-right">Title</label>
      <h3 id="title" class="col-span-3">{article.title}</h3>
    </div>
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="link" class="text-right">Link</label>
      <h3 id="link" class="col-span-3">{article.link}</h3>
    </div>
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="published" class="text-right">Published</label>
      <h3 id="published" class="col-span-3">{article['@timestamp']}</h3>
    </div>
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="type" class="text-right">Type</label>
      <h3 id="type" class="col-span-3">{article.type}</h3>
    </div>
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="criticality" class="text-right">Criticality</label>
      <h3 id="criticality" class="col-span-3">{article.criticality}</h3>
    </div>
    <div class="grid grid-cols-4 items-center gap-4">
      <label for="urgency" class="text-right">Urgency</label>
      <h3 id="urgency" class="col-span-3">{article.urgency}</h3>
    </div>

    <div class="grid grid-cols-4 items-center gap-4">
      <label for="industries" class="text-right mt-0">Industries</label>
      <div id="industries">
        {#each article.affected_industries as industry}
          <p>{industry}</p>
        {/each}
      </div>
    </div>

    <div class="grid grid-cols-4 items-center gap-4">
      <label for="tactics" class="text-right mt-0">Mitre Tactics</label>
      <div id="tactics">
        {#each article.tactics as tactic}
          <p>{tactic}</p>
        {/each}
      </div>
    </div>

    <div class="grid grid-cols-4 items-center gap-4">
      <label for="organizations" class="text-right mt-0">Organizations</label>
      <div id="organizations">
        {#each article.organizations as organization}
          <p>{organization}</p>
        {/each}
      </div>
    </div>

    <div class="grid grid-cols-4 items-center gap-4">
      <label for="tags" class="text-right mt-0">Tags</label>
      <div id="tags">
        {#each article.tags as tag}
          <p>{tag}</p>
        {/each}
      </div>
    </div>
  </div>
</ScrollArea>

