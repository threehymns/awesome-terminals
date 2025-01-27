<script lang="ts">
	import '../app.css';
	import { ScrollArea } from "$lib/components/ui/scroll-area/index.js";
	import { ModeWatcher } from 'mode-watcher';
	import "@fontsource/fira-sans";
	let { children } = $props();
</script>

<ModeWatcher />
<ScrollArea class="h-screen">
	{@render children()}
</ScrollArea>

<style>
	:global(body) {
		font-family: "Fira Sans", system-ui, sans-serif;
	}
</style>