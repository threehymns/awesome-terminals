<script lang="ts">
	import terminals from '../terminals.json';
	import { Button } from '$lib/components/ui/button';
	import { Card, CardContent, CardHeader, CardTitle } from '$lib/components/ui/card';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';
	import * as Tooltip from '$lib/components/ui/tooltip/index.js';
	import { Search, SearchX, ArrowDownUp } from 'lucide-svelte';
	import LightSwitch from '@/components/ui/light-switch.svelte';
	import { flip } from 'svelte/animate';
	import { scale, fade } from 'svelte/transition';
	import Fuse from 'fuse.js';
	import { Separator } from '@/components/ui/separator';
	import {
		Select,
		SelectContent,
		SelectGroup,
		SelectItem,
		SelectTrigger
	} from '@/components/ui/select';

	// State
	let searchTerm = '';
	let activeFilters = new Set();
	let sortOption = 'default'; // Default sort option

	// Emoji mapping
	const emojiMap = {
		Windows: '🪟',
		macOS: '🍎',
		Linux: '🐧',
		Android: '📱',
		'Open Source': '📘',
		Proprietary: '📕',
		GPU: '⚡',
		Electron: '🌐',
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
		? fuse
				.search(searchTerm)
				.map((result) => result.item)
				.filter((term) => {
					const matchesFilters =
						activeFilters.size === 0 ||
						Array.from(activeFilters).every(
							(filter) => term.tags.includes(filter) || term.platforms.includes(filter)
						);
					return matchesFilters;
				})
		: terminals.filter((term) => {
				const matchesFilters =
					activeFilters.size === 0 ||
					Array.from(activeFilters).every(
						(filter) => term.tags.includes(filter) || term.platforms.includes(filter)
					);
				return matchesFilters;
			});

	$: sortedTerminals = (() => {
		// Start with the filtered terminals
		let terminals = filteredTerminals;

		// Apply sorting based on selected option
		switch (sortOption) {
			case 'default':
			default:
				return terminals; // Return original filtered list
			case 'name-asc':
				return [...terminals].sort((a, b) => a.name.localeCompare(b.name));
			case 'name-desc':
				return [...terminals].sort((a, b) => b.name.localeCompare(a.name));
		}
	})();

	function toggleFilter(tag) {
		activeFilters.has(tag) ? activeFilters.delete(tag) : activeFilters.add(tag);
		activeFilters = new Set(activeFilters);
	}
</script>

<div class="min-h-screen bg-zinc-100 p-12 transition-colors duration-300 dark:bg-black">
	<div class="mx-auto max-w-6xl">
		<h1
			class="mb-12 w-fit bg-gradient-to-r from-zinc-900 to-indigo-600 bg-clip-text text-4xl font-extrabold tracking-tight text-transparent dark:from-zinc-100 dark:to-indigo-500"
		>
			Awesome Terminals
		</h1>

		<!-- Search and Filters -->
		<div class="mb-12 space-y-6">
			<div class="flex gap-3">
				<Select type="single" bind:value={sortOption}>
					<SelectTrigger class="relative h-11 w-[180px] pl-9">
						<ArrowDownUp class="absolute left-3 h-4 w-4" />
						{#if sortOption === 'default'}
							Default
						{:else if sortOption === 'name-asc'}
							Name (A to Z)
						{:else if sortOption === 'name-desc'}
							Name (Z to A)
						{/if}
					</SelectTrigger>
					<SelectContent>
						<SelectGroup>
							<Label>Sort Options</Label>
							<SelectItem value="default">Default</SelectItem>
							<SelectItem value="name-asc">Name (A to Z)</SelectItem>
							<SelectItem value="name-desc">Name (Z to A)</SelectItem>
						</SelectGroup>
					</SelectContent>
				</Select>
				<div class="relative grow">
					<div
						class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-zinc-400 dark:text-zinc-500"
					>
						<Search class="h-5 w-5 transition-colors" />
					</div>
					<Input
						type="text"
						bind:value={searchTerm}
						placeholder="Filter terminals..."
						class="h-11 w-full rounded-lg border-zinc-200 bg-white pl-10 transition-all focus-visible:border-indigo-500 focus-visible:ring-2 focus-visible:ring-indigo-500/50 dark:border-zinc-800 dark:bg-zinc-900"
					/>
				</div>
				<LightSwitch />
			</div>

			<div class="flex flex-wrap items-center gap-2">
				{#each Object.keys(emojiMap) as tag}
					<button
						on:click={() => toggleFilter(tag)}
						class:active={activeFilters.has(tag)}
						class="flex items-center rounded-full border px-3 py-1.5 text-sm font-medium outline-hidden
                   transition-all hover:scale-[1.02] focus-visible:ring-2 focus-visible:ring-indigo-500 focus-visible:ring-offset-2 active:scale-[0.98] dark:focus-visible:ring-offset-black
                   {activeFilters.has(tag)
							? 'border-indigo-600 bg-indigo-500 text-white shadow-lg shadow-indigo-500/20 dark:bg-indigo-950'
							: 'border-zinc-200 bg-white text-zinc-700 hover:border-indigo-300 dark:border-zinc-700 dark:bg-zinc-800 dark:text-zinc-200 dark:hover:border-indigo-600'}"
					>
						<span class="mr-1">{emojiMap[tag]}</span>
						{tag.replace('-', ' ')}
					</button>
				{/each}
			</div>

			<!-- Terminal Grid -->
			<div class="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3">
				{#each sortedTerminals as term (term.name)}
					<div
						animate:flip={{ duration: 300 }}
						transition:scale={{ duration: 300 }}
						key={term.name}
					>
						<Card class="h-fit transition-colors" key={term.name}>
							<CardHeader>
								<div class="mb-2 flex justify-between">
									<h2 class="text-xl font-semibold">
										<a
											href={term.url}
											target="_blank"
											rel="noreferrer"
											class="hover:text-indigo-600 hover:underline hover:underline-offset-4 dark:hover:text-indigo-400"
										>
											{term.name}
										</a>
									</h2>
									<div class="flex space-x-1">
										{#each term.platforms as platform}
											<Tooltip.Provider>
												<Tooltip.Root>
													<Tooltip.Trigger
														class="text-xl transition-all hover:scale-110"
														onclick={() => toggleFilter(platform)}
													>
														{emojiMap[platform]}
														<Tooltip.Content>{platform}</Tooltip.Content>
													</Tooltip.Trigger>
												</Tooltip.Root>
											</Tooltip.Provider>
										{/each}
									</div>
								</div>
								<p class="mb-2 text-sm text-zinc-600 dark:text-zinc-400">{term.description}</p>
							</CardHeader>
							<CardContent>
								<div class="mb-4 flex flex-wrap gap-2">
									{#each term.tags as tag}
										<button
											class="flex items-center rounded-full bg-zinc-100 px-2 py-1 text-xs dark:bg-zinc-800"
											on:click={() => toggleFilter(tag)}
										>
											{emojiMap[tag]} <span class="ml-1">{tag.replace('-', ' ')}</span>
										</button>
									{/each}
								</div>
								<ul class="space-y-1 text-sm text-zinc-600 dark:text-zinc-400">
									{#each term.features as feature}
										<li class="flex items-center">
											<svg
												class="mr-2 h-4 w-4 text-green-500"
												fill="currentColor"
												viewBox="0 0 20 20"
											>
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
					</div>
				{:else}
					<div
						class="col-span-full flex flex-col items-center justify-center py-16 text-center absolute right-1/2 translate-x-1/2"
						in:fade
					>
						<SearchX
							strokeWidth={1}
							class="mb-4 h-24 w-24 text-zinc-600 dark:text-zinc-400 animate-pulse"
						/>
						<h2 class="mb-4 text-2xl font-bold text-zinc-700 dark:text-zinc-300">
							No Terminals Found
						</h2>
						<p class="mb-6 max-w-md text-zinc-600 dark:text-zinc-400">
							Try broadening your search or filters. We have a diverse collection of terminals
							waiting to be discovered!
						</p>
						<div class="flex space-x-4">
							<Button
								variant="outline"
								onclick={() => {
									searchTerm = '';
									activeFilters = new Set();
								}}
							>
								Clear Filters
							</Button>
							<Button
								variant="secondary"
								href="https://github.com/threehymns/awesome-terminals/issues/new"
								target="_blank"
							>
								Suggest a Terminal
							</Button>
						</div>
					</div>
				{/each}
			</div>
		</div>
	</div>
</div>
