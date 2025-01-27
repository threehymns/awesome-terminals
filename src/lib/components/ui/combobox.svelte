<script lang="ts">
  import { createEventDispatcher } from "svelte";
  import Check from "svelte-radix/Check.svelte";
  import CaretSort from "svelte-radix/CaretSort.svelte";
  import { tick } from "svelte";
  import * as Command from "$lib/components/ui/command/index.js";
  import * as Popover from "$lib/components/ui/popover/index.js";
  import { Button } from "$lib/components/ui/button/index.js";
  import { cn } from "$lib/utils.js";
  
  export let items: Array<{ value: string; label: string }> = [];
  export let value = "";
  export let onSelect: (value: string) => void = () => {};
  
  const dispatch = createEventDispatcher();
  
  $: selectedValue = items.find((i) => i.value === value)?.label ?? "";
  
  // We want to refocus the trigger button when the user selects
  // an item from the list so users can continue navigating the
  // rest of the form with the keyboard.
  function closeAndFocusTrigger(triggerId: string) {
   dispatch("close");
   tick().then(() => {
    document.getElementById(triggerId)?.focus();
   });
  }
 </script>
  
 <Popover.Root bind:open on:close={() => dispatch("close")} let:ids>
  <Popover.Trigger asChild let:builder>
   <Button
    builders={[builder]}
    variant="outline"
    role="combobox"
    aria-expanded={open}
    class="w-[200px] justify-between"
   >
    {selectedValue}
    <CaretSort class="ml-2 h-4 w-4 shrink-0 opacity-50" />
   </Button>
  </Popover.Trigger>
  <Popover.Content class="w-[200px] p-0">
   <Command.Root>
    <Command.Input placeholder="Search" class="h-9" />
    <Command.Empty>No item found.</Command.Empty>
    <Command.Group>
     {#each items as item}
      <Command.Item
       value={item.value}
       onSelect={(currentValue) => {
        value = currentValue;
        onSelect(value);
        closeAndFocusTrigger(ids.trigger);
       }}
      >
       <Check
        class={cn(
         "mr-2 h-4 w-4",
         value !== item.value && "text-transparent"
        )}
       />
       {item.label}
      </Command.Item>
     {/each}
    </Command.Group>
   </Command.Root>
  </Popover.Content>
 </Popover.Root>