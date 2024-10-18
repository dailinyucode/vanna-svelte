<script lang="ts">
    import InChatButton from "./InChatButton.svelte";

    export let onSubmit: (chart_instructions: string) => void; // a callback function that will be called on Submit

    let chart_instructions: string;
</script>

<textarea bind:value={chart_instructions} class="block p-2.5 w-full text-blue-600 hover:text-blue-500 text-2xl dark:text-blue-500 dark:hover:text-blue-400 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500 font-mono" placeholder="使用红色的线条画折线图"></textarea>

<InChatButton message="运行" onSubmit={() => onSubmit(chart_instructions)} />