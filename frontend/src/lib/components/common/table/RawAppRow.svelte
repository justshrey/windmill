<script lang="ts">
	import Dropdown from '$lib/components/Dropdown.svelte'
	import type MoveDrawer from '$lib/components/MoveDrawer.svelte'
	import SharedBadge from '$lib/components/SharedBadge.svelte'
	import type ShareModal from '$lib/components/ShareModal.svelte'
	import { RawAppService, type ListableRawApp } from '$lib/gen'
	import { userStore, workspaceStore } from '$lib/stores'
	import { faEdit, faFileExport, faShare, faTrashAlt } from '@fortawesome/free-solid-svg-icons'
	import { createEventDispatcher } from 'svelte'
	import Button from '../button/Button.svelte'
	import Row from './Row.svelte'
	import Drawer from '../drawer/Drawer.svelte'
	import DrawerContent from '../drawer/DrawerContent.svelte'
	import FileInput from '../fileInput/FileInput.svelte'
	import { goto } from '$app/navigation'
	import type DeployWorkspaceDrawer from '$lib/components/DeployWorkspaceDrawer.svelte'

	export let app: ListableRawApp & { canWrite: boolean }
	export let marked: string | undefined
	export let starred: boolean
	export let shareModal: ShareModal
	export let moveDrawer: MoveDrawer
	export let deleteConfirmedCallback: (() => void) | undefined
	export let deploymentDrawer: DeployWorkspaceDrawer
	export let depth: number = 0

	let updateAppDrawer: Drawer

	let { summary, version, path, extra_perms, workspace_id, canWrite } = app

	const dispatch = createEventDispatcher()
</script>

<Drawer bind:this={updateAppDrawer} size="800px">
	<DrawerContent title="Update app" on:close={() => updateAppDrawer?.toggleDrawer?.()}>
		<FileInput
			accept={'.js'}
			multiple={false}
			convertTo={'text'}
			iconSize={24}
			class="text-sm py-4"
			on:change={async ({ detail }) => {
				await RawAppService.updateRawApp({
					workspace: $workspaceStore ?? '',
					path,
					requestBody: { value: detail?.[0] }
				})
				goto(`/apps/get_raw/${version + 1}/${path}`)
			}}
		/>
	</DrawerContent>
</Drawer>

<Row
	href="/apps/get_raw/{version}/{path}"
	kind="raw_app"
	{marked}
	{path}
	{summary}
	workspaceId={workspace_id ?? $workspaceStore ?? ''}
	{starred}
	on:change
	canFavorite={true}
	{depth}
>
	<svelte:fragment slot="badges">
		<SharedBadge {canWrite} extraPerms={extra_perms} />
	</svelte:fragment>
	<svelte:fragment slot="actions">
		<span class="hidden md:inline-flex gap-x-1">
			{#if !$userStore?.operator}
				{#if canWrite}
					<div>
						<Button
							color="light"
							size="xs"
							variant="border"
							startIcon={{ icon: faEdit }}
							on:click={() => updateAppDrawer?.toggleDrawer?.()}
						>
							Edit
						</Button>
					</div>
				{/if}
			{/if}
		</span>
		<Dropdown
			placement="bottom-end"
			dropdownItems={() => {
				return [
					{
						displayName: 'Move/Rename',
						icon: faFileExport,
						action: () => {
							moveDrawer.openDrawer(path, summary, 'raw_app')
						},
						disabled: !canWrite
					},
					{
						displayName: 'Move/Rename',
						icon: faFileExport,
						action: () => {
							moveDrawer.openDrawer(path, summary, 'raw_app')
						},
						disabled: !canWrite
					},
					{
						displayName: 'Deploy to prod/staging',
						icon: faFileExport,
						action: () => {
							deploymentDrawer.openDrawer(path, 'raw_app')
						}
					},
					{
						displayName: canWrite ? 'Share' : 'See Permissions',
						icon: faShare,
						action: () => {
							shareModal.openDrawer && shareModal.openDrawer(path, 'raw_app')
						}
					},
					{
						displayName: 'Delete',
						icon: faTrashAlt,
						action: async (event) => {
							if (event?.shiftKey) {
								await RawAppService.deleteRawApp({ workspace: $workspaceStore ?? '', path })
								dispatch('change')
							} else {
								deleteConfirmedCallback = async () => {
									await RawAppService.deleteRawApp({ workspace: $workspaceStore ?? '', path })
									dispatch('change')
								}
							}
						},
						type: 'delete',
						disabled: !canWrite
					}
				]
			}}
		/>
	</svelte:fragment>
</Row>
