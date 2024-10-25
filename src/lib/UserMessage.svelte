<script>
    import Text from "./Text.svelte";
    import doctor from "../assets/doctor.png";

    /** @type {string} */
    export let message;

</script>

<!-- Chat Bubble -->
      <li class="py-2 sm:py-4">
        <div class="max-w-4xl px-4 sm:px-6 lg:px-8 mx-auto">
          <div class="max-w-2xl flex gap-x-2 sm:gap-x-4">
            <img src="{doctor}" class="flex-shrink-0 w-[2.375rem] h-[2.375rem] " alt="agent logo" >

            <div class="grow mt-2 space-y-3">
              <Text>
                {message}
              </Text>
              <slot />
            </div>
          </div>
        </div>
      </li>
      <!-- End Chat Bubble -->