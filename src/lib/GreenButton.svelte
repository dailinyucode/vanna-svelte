<script lang="ts">
    export let message: string;
    export let onSubmit: (message: string) => void; // a callback function that will be called on Submit

    function handleClick() {
        onSubmit(message);
    }
</script>

<button type="button" on:click={handleClick} class="mb-2.5 mr-1.5 py-3 px-4 inline-flex justify-center items-center gap-2 rounded-md border-2 border-blue-200 font-semibold text-blue-500 hover:text-white hover:bg-blue-500 hover:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-200 focus:ring-offset-2 transition-all text-sm dark:focus:ring-offset-gray-800">
    {message}
</button>