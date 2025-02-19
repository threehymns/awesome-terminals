<script lang="ts">
	import Sun from 'svelte-radix/Sun.svelte';
	import Moon from 'svelte-radix/Moon.svelte';
	import { toggleMode } from 'mode-watcher';
	import { Button } from '$lib/components/ui/button/index.js';
</script>

<Button onclick={toggleMode} class="relative h-11 w-11" variant="outline">
	<Sun
		class="h-[1.2rem] w-[1.2rem] scale-100 rotate-0 text-zinc-900 transition-transform duration-300 ease-in-out dark:scale-0 dark:-rotate-90"
	/>
	<Moon
		class="absolute h-[1.2rem] w-[1.2rem] scale-0 rotate-90 text-white transition-transform duration-300 ease-in-out dark:scale-100 dark:rotate-0"
	/>
	<span class="sr-only">Toggle theme</span>
</Button>
