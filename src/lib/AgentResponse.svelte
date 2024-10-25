<script lang="ts">
import ai from '../assets/ai.png'
</script>

<li class="max-w-4xl py-2 px-4 sm:px-6 lg:px-8 mx-auto flex gap-x-2 sm:gap-x-4">
    <img src="{ai}" class="flex-shrink-0 w-[2.375rem] h-[2.375rem] " alt="agent logo" >
    
    <div class="space-y-3 overflow-x-auto overflow-y-hidden">
        <slot />
    </div>
</li> 