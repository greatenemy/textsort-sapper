<style>

</style>

<svelte:head>
  <title>Text Sort</title>
</svelte:head>

<h2 class="text-center">Text Sort</h2>
<br />

<div class="container">
  {#if process.browser}
    <TextSort />
  {/if}
</div>

<script>
  import TextSort from '~/components/TextSort.svelte';
</script>
