<script>
  import { writable } from 'svelte/store';
  import {get} from './registry';
  export let engineId;
  export let data;
  export let items;

  const store = writable();
  $: store.set(get(engineId, data));
</script>

{#if $store.ok}
  {#if $store.valid}
    {#if items}
      <svelte:component this={$store.component} {...$store.properties}>
        {#each items as item}
          <svelte:self engineId={item.type} data={item.data} items={item.items} />
        {/each}
      </svelte:component>
    {:else}
      <svelte:component this={$store.component} {...$store.properties} />
    {/if}
  {:else}
    <div class="error">Something is wrong with the data for <strong>{engineId}</strong>, please ensure it matches its associated schema</div>
    <details>
      <summary>Errors</summary>
      <pre>{JSON.stringify($store.errors, null, 2)}</pre>
    </details>
  {/if}
{:else}
  <div class="error"><strong>{engineId}</strong> is not registered, please ensure that it is in the bootstrap file</div>
{/if}

<style>
  .error {
    color: red;
  }
</style>