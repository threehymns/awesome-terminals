<script lang="ts">
	import terminals from '../terminals.json';
	import { Button } from '$lib/components/ui/button';
	import { Card, CardContent, CardHeader, CardTitle } from '$lib/components/ui/card';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';
  import LightSwitch from '@/components/ui/light-switch.svelte';
  import autoAnimate from '@formkit/auto-animate';
  import Fuse from 'fuse.js';

	// State
	let searchTerm = '';
	let activeFilters = new Set();

	// Emoji mapping
	const emojiMap = {
		Windows: '🪟',
		macOS: '🍎',
		Linux: '🐧',
		Android: '📱',
		'Open Source': '📘',
		'Proprietary': '📕',
		GPU: '⚡',
		'Electron': '🌐',
		Theming: '🎨',
		AI: '🤖',
    'File Previews': '📁',
    Wayland: '✨'
	};

	// Fuzzy search configuration
	const fuseOptions = {
		keys: ['name', 'description', 'tags', 'features', 'platforms'],
		threshold: 0.4, // Adjust sensitivity (0 = exact match, 1 = very loose)
		includeScore: true
	};

	// Create Fuse instance
	$: fuse = new Fuse(terminals, fuseOptions);

	// Filtered terminals with fuzzy search
	$: filteredTerminals = searchTerm
		? fuse.search(searchTerm).map(result => result.item).filter((term) => {
			const matchesFilters =
				activeFilters.size === 0 || 
				Array.from(activeFilters).every((filter) => 
					term.tags.includes(filter) || term.platforms.includes(filter)
				);
			return matchesFilters;
		})
		: terminals.filter((term) => {
			const matchesFilters =
				activeFilters.size === 0 || 
				Array.from(activeFilters).every((filter) => 
					term.tags.includes(filter) || term.platforms.includes(filter)
				);
			return matchesFilters;
		});

	function toggleFilter(tag) {
		activeFilters.has(tag) ? activeFilters.delete(tag) : activeFilters.add(tag);
		activeFilters = new Set(activeFilters);
	}
</script>

<div class="min-h-screen bg-zinc-100 p-8 dark:bg-zinc-900">
	<div class="mx-auto max-w-6xl">
    <h1 class="mb-8 text-3xl font-bold text-center">Awesome Terminals</h1>
		<!-- Search and Filters -->
		<div class="mb-8 space-y-4">
			<div class="flex space-x-2">
        <Input type="text" bind:value={searchTerm} placeholder="Filter terminals..." class="" />
        <LightSwitch />
      </div>

			<div class="flex flex-wrap gap-2">
				{#each Object.keys(emojiMap) as tag}
					<button
						on:click={() => toggleFilter(tag)}
						class:active={activeFilters.has(tag)}
						class="flex items-center rounded-full border px-4 py-2 transition-colors {activeFilters.has(
							tag
						)
							? 'border-blue-600 bg-blue-500 text-white'
							: 'border-zinc-300 bg-white text-zinc-700 hover:border-blue-400 dark:border-zinc-700 dark:bg-zinc-800 dark:text-zinc-200'}"
					>
						<span class="mr-2">{emojiMap[tag]}</span>
						{tag.replace('-', ' ')}
					</button>
				{/each}
			</div>
		</div>

		<!-- Terminal Grid -->
		<div class="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3" use:autoAnimate>
			{#each filteredTerminals as term (term.name)}
				<Card class="h-fit" key={term.name}>
					<CardHeader>
						<div class="mb-2 flex justify-between">
							<h2 class="text-xl font-semibold">
								<a
									href={term.url}
									target="_blank"
									rel="noreferrer"
									class="transition-colors hover:text-blue-600 dark:hover:text-blue-400"
								>
									{term.name}
								</a>
							</h2>
							<div class="flex space-x-1">
								{#each term.platforms as platform}
									<span class="text-xl" title={platform}>{emojiMap[platform]}</span>
								{/each}
							</div>
						</div>
						<p class="mb-2 text-sm text-zinc-600 dark:text-zinc-400">{term.description}</p>
					</CardHeader>

					<CardContent>
						<div class="mb-4 flex flex-wrap gap-2">
							{#each term.tags as tag}
								<span
									class="flex items-center rounded-full bg-zinc-100 px-2 py-1 text-sm dark:bg-zinc-700"
								>
									{emojiMap[tag]} <span class="ml-1">{tag.replace('-', ' ')}</span>
								</span>
							{/each}
						</div>
						<ul class="space-y-1 text-sm text-zinc-600 dark:text-zinc-400">
							{#each term.features as feature}
								<li class="flex items-center">
									<svg class="mr-2 h-4 w-4 text-green-500" fill="currentColor" viewBox="0 0 20 20">
										<path
											fill-rule="evenodd"
											d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
											clip-rule="evenodd"
										/>
									</svg>
									{feature}
								</li>
							{/each}
						</ul>
					</CardContent>
				</Card>
			{:else}
				<div class="col-span-full text-center py-12 text-zinc-500 dark:text-zinc-400">
					No terminals found matching your criteria
				</div>
			{/each}
		</div>
	</div>
</div>
