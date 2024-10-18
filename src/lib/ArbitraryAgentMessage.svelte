<script lang="ts">
    import { onMount } from 'svelte';
    import type { ApiData, MessageContents } from './types.ts';
    import InChatButton from './InChatButton.svelte';
    import SlowReveal from './SlowReveal.svelte';

    // declare all possible properties
    export let endpoint: string;
    export let addMessage: (msg: string) => void; 

    let apiStatus: ApiData<MessageContents> = { status: 'NotRequested' };

    onMount(async () => {
      try {
        apiStatus = { status: 'Loading' };

        // Generate query string from props
        const queryString = Object.entries($$props)
          .filter(([key, _]) => key !== 'endpoint' && key !== 'addMessage') // Exclude 'endpoint' from the query string
          .map(([key, value]) => `${encodeURIComponent(key)}=${encodeURIComponent(value)}`)
          .join('&');

        const response = await fetch(`${import.meta.env.VITE_API_URL}/api/v1/${endpoint}?${queryString}`);
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const data: MessageContents = await response.json();
        apiStatus = { status: 'Loaded', data };
      } catch (error) {
        apiStatus = { status: 'Error', error: "Put the error here" };
      }
    });
</script>

{#if apiStatus.status === 'NotRequested'}
  <p>Data has not been requested yet.</p>
{:else if apiStatus.status === 'Loading'}
<li class="max-w-4xl py-2 px-4 sm:px-6 lg:px-8 mx-auto flex gap-x-2 sm:gap-x-4">
    <img src="/vanna.svg" class="flex-shrink-0 w-[2.375rem] h-[2.375rem] animate-bounce" alt="agent logo" >
    
    <div class="space-y-3">
        <h2 class="font-medium text-gray-800 dark:text-white">
        Loading...
        </h2>
    </div>
</li>
{:else if apiStatus.status === 'Loaded'}
<li class="max-w-4xl py-2 px-4 sm:px-6 lg:px-8 mx-auto flex gap-x-2 sm:gap-x-4">
    <img src="/vanna.svg" class="flex-shrink-0 w-[2.375rem] h-[2.375rem] " alt="agent logo" >
    
    <div class="space-y-3">
        <h2 class="font-medium text-gray-800 dark:text-white">
            {#if apiStatus.data.type === 'sql'}
                <p class="text-gray-800 dark:text-white">
                    <SlowReveal text={apiStatus.data.text} />
                </p>
            {:else if apiStatus.data.type === 'question_list'}
                <p class="text-gray-800 dark:text-white">
                    {apiStatus.data.header}
                    {#each apiStatus.data.questions as question}
                        <InChatButton message={question} onSubmit={addMessage} />
                    {/each}
                </p>
            {:else}
                <p>I don't know what to do with type {apiStatus.data.type}</p>
            {/if}
        </h2>
    </div>
</li> 
{:else if apiStatus.status === 'Error'}
  <p>An error occurred: {apiStatus.error}</p>
{/if}
