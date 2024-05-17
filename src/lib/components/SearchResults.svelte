 <script lang="ts">
  import * as Table from "$lib/components/ui/table";
  import { searchResultsStore } from "/src/stores/searchResultsStore.ts";
  import * as Dialog from "$lib/components/ui/dialog";
  import ArticleInfo from "$lib/components/ArticleInfo.svelte";

  function parsePosition(str){
    return Math.min(...str.replaceAll(' ', '').replaceAll('[', '').replaceAll(']', '').split(',').map(function(str) { return parseInt(str); }));
  }

  function computeOpacity(position){
    return Math.floor(position / 2);
  }
</script>

<Table.Root>
  <Table.Caption>Matched articles.</Table.Caption>
  <Table.Header>
    <Table.Row>
      <Table.Head class="w-[100px]">Published</Table.Head>
      <Table.Head>Title</Table.Head>
      <Table.Head>Link</Table.Head>
      <Table.Head class="text-right">Type</Table.Head>
      <Table.Head>Criticality</Table.Head>
      <Table.Head>Urgency</Table.Head>
      <Table.Head>Info</Table.Head>
    </Table.Row>
  </Table.Header>
  <Table.Body>
    {#each $searchResultsStore as result}
      <Table.Row>
        <Table.Cell class="font-medium">{result["@timestamp"]}</Table.Cell>
        <Table.Cell><a href={result.link}>{result.title}</a></Table.Cell>
        <Table.Cell><a href={result.link}>{result.link}</a></Table.Cell>
        <Table.Cell class="text-right">{result.type}</Table.Cell>
        <Table.Cell>{result.criticality}</Table.Cell>
        <Table.Cell>{result.urgency}</Table.Cell>
        <Table.Cell>
          <Dialog.Root>
            <Dialog.Trigger>Info</Dialog.Trigger>
            <Dialog.Content class="bg-white h-2/3">
              <Dialog.Header>
                <Dialog.Title>{result.title}</Dialog.Title>
                <Dialog.Description>
                    <ArticleInfo article={result} />
                </Dialog.Description>
              </Dialog.Header>
            </Dialog.Content>
          </Dialog.Root>
        </Table.Cell>
      </Table.Row>
    {/each}
  </Table.Body>
</Table.Root>


