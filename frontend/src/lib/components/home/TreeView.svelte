<script lang="ts">
	import { ChevronDown, ChevronUp, Folder, FolderTree, User } from 'lucide-svelte'
	import Item from './Item.svelte'
	import type { FolderItem, ItemType, UserItem } from './treeViewUtils'
	import { twMerge } from 'tailwind-merge'
	import { slide } from 'svelte/transition'
	import { pluralize } from '$lib/utils'

	export let item: ItemType | FolderItem | UserItem | undefined

	export let depth: number = 0

	const isFolder = (i: any): i is FolderItem => i && 'folderName' in i
	const isUser = (i: any): i is UserItem => i && 'username' in i

	let opened: boolean = true
</script>

{#if item}
	{#if isFolder(item)}
		<div>
			<div class={twMerge('px-4 py-2 border-b w-full flex flex-row items-center justify-between')}>
				<div
					class={twMerge('flex flex-row items-center gap-4 text-sm font-semibold')}
					style={depth > 0 ? `padding-left: ${depth * 16}px;` : ''}
				>
					<div
						class=" rounded-md p-1 flex justify-center items-center border bg-gray-50 border-gray-200 dark:bg-transparent dark:border-gray-900"
					>
						{#if depth === 0}
							<Folder size={20} color="#aaa" />
						{:else}
							<FolderTree size={20} color="#aaa" />
						{/if}
					</div>

					<div>
						{#if depth === 0}f/{/if}{item.folderName}
						<div class="text-2xs font-normal text-secondary">
							({pluralize(item.items.length, ' item')})
						</div>
					</div>
				</div>
				<button on:click={() => (opened = !opened)}>
					{#if opened}
						<ChevronUp size={20} />
					{:else}
						<ChevronDown size={20} />
					{/if}
				</button>
			</div>
			{#if opened}
				<div transition:slide>
					{#each item.items as subItem}
						<svelte:self
							item={subItem}
							on:scriptChanged
							on:flowChanged
							on:appChanged
							on:rawAppChanged
							on:reload
							depth={depth + 1}
						/>
					{/each}
				</div>
			{/if}
		</div>
	{:else if isUser(item)}
		<div>
			<div class={twMerge('px-4 py-2 border-b w-full flex flex-row items-center justify-between')}>
				<div
					class={twMerge('flex flex-row items-center gap-4 text-sm font-semibold')}
					style={depth > 0 ? `padding-left: ${depth * 16}px;` : ''}
				>
					<div
						class=" rounded-md p-1 flex justify-center items-center border bg-gray-50 border-gray-200 dark:bg-transparent dark:border-gray-900"
					>
						<User size={20} color="#aaa" />
					</div>

					<div>
						u/{item.username}
						<div class="text-2xs font-normal text-secondary"
							>({pluralize(item.items.length, ' item')})</div
						>
					</div>
				</div>
				<button on:click={() => (opened = !opened)}>
					{#if opened}
						<ChevronUp size={20} />
					{:else}
						<ChevronDown size={20} />
					{/if}
				</button>
			</div>
			{#if opened}
				<div transition:slide>
					{#each item.items as subItem}
						<svelte:self
							item={subItem}
							on:scriptChanged
							on:flowChanged
							on:appChanged
							on:rawAppChanged
							on:reload
							depth={depth + 1}
						/>
					{/each}
				</div>
			{/if}
		</div>
	{:else}
		<Item {item} on:scriptChanged on:flowChanged on:appChanged on:rawAppChanged on:reload {depth} />
	{/if}
{/if}
