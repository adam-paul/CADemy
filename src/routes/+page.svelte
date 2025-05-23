<script lang="ts">
	// Component imports
	import MapCanvas from '$lib/components/MapCanvas.svelte';

	// Type & constant imports
	import type { SubjectMapData } from '$lib/types/map';
	import type { Subject } from '$lib/types/subjects';
	import { SUBJECTS } from '$lib/types/subjects';

	// Utility imports
	import { getMapData } from '$lib/mapData';
	import { minigameStore } from '$lib/stores/minigameStore';
	import { client } from '$lib/auth-client';

	const session = client.useSession();

	let selectedSubject: Subject = SUBJECTS[0];
	let currentMapData: SubjectMapData | undefined;

	$: isLoadingMinigame = $minigameStore.isLoading;
	$: currentMinigameComponent = $minigameStore.activeComponent;
	$: minigameError = $minigameStore.error;

	$: currentMapData = getMapData(selectedSubject);
	$: {
		if (selectedSubject) {
			minigameStore.closeActiveMinigame(); // When subject changes, ensure any active minigame is closed
			minigameStore.selectNode(null); // Clear node selection
			clearNodeInfoPopup(); // Also clear the page's popup
		}
	}

	function selectSubject(subject: Subject) {
		selectedSubject = subject;
	}

	// Node interaction display (Page level popup)
	let currentNodeInfo: string | null = null;

	function clearNodeInfoPopup() {
		currentNodeInfo = null;
	}

	// Interaction handler: determines node type and updates store/popup
	function handleNodeInteractionProp(nodeName: string) {
		clearNodeInfoPopup();

		const node = currentMapData?.nodes.find((n) => n.name === nodeName);

		if (!node) {
			console.error(`Node data not found for name: ${nodeName}`);
			minigameStore.selectNode(null); // Clear store selection
			currentNodeInfo = 'Error: Node not found.';
			return;
		}

		// Update the store with the selected node (minigame or not)
		minigameStore.selectNode(node);

		// Update the page-level info popup based on the node type
		if (node.minigame) {
			currentNodeInfo = `Press Enter to play: ${node.name}`;
		} else {
			currentNodeInfo = `Lesson: ${node.name}`;
		}
	}

	function handleKeydown(event: KeyboardEvent) {
		// Use $minigameStore to access current state directly if needed, or rely on actions
		if ($minigameStore.activeComponent) {
			// If minigame is open, tell store to close it on Escape
			if (event.key === 'Escape') {
				minigameStore.closeActiveMinigame();
			}
		} else if ($minigameStore.selectedNode && event.key === 'Enter') {
			// If not in a minigame, but a minigame node is selected, tell store to load it
			event.preventDefault();
			minigameStore.loadSelectedMinigame();
		}
	}

	async function handleSignOut() {
		await client.signOut();
		window.location.reload();
	}
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="app-container">
	<header>
		<div class="header-content">
			<div class="header-left">
				<h1>Incept Layer 2 - Overworld</h1>
				<nav class="subject-tabs">
					{#each SUBJECTS as subject (subject)}
						<button
							on:click={() => selectSubject(subject)}
							class:active={selectedSubject === subject}
						>
							{subject}
						</button>
					{/each}
				</nav>
			</div>
			<div class="user-info">
				<span class="username">
					{$session.data?.user?.name || $session.data?.user?.email}
				</span>
				<button class="logout-button" on:click={handleSignOut}>Log Out</button>
			</div>
		</div>
	</header>

	<main>
		{#if currentMapData}
			<MapCanvas mapData={currentMapData} onNodeInteract={handleNodeInteractionProp} />
		{:else}
			<p>Select a subject to view the map.</p>
		{/if}

		<!-- Page-level Node Interaction / Store Error Popup -->
		{#if minigameError || currentNodeInfo}
			<div class="node-info-popup {minigameError ? 'error' : ''}">
				{minigameError || currentNodeInfo}
			</div>
		{/if}

		<!-- Use store state for Loading Indicator -->
		{#if isLoadingMinigame}
			<div class="loading-overlay">
				<div class="loading-indicator">Loading Minigame...</div>
			</div>
		{/if}

		<!-- Use store state for Minigame Display Area -->
		{#if currentMinigameComponent}
			<div class="minigame-overlay">
				<svelte:component this={currentMinigameComponent} />
			</div>
		{/if}
	</main>
</div>

<style>
	.app-container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		width: 100vw;
		overflow: hidden;
	}
	header {
		padding: 0.5rem 1rem;
		background-color: #eee;
		border-bottom: 1px solid #ccc;
	}
	.header-content {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.header-left {
		flex: 1;
	}
	.user-info {
		display: flex;
		align-items: center;
		gap: 1rem;
	}
	.username {
		font-weight: 500;
	}
	.logout-button {
		padding: 0.4rem 0.8rem;
		border: 1px solid #ccc;
		background-color: #fff;
		cursor: pointer;
		border-radius: 4px;
	}
	.logout-button:hover {
		background-color: #f0f0f0;
	}
	.subject-tabs button {
		padding: 0.5rem 1rem;
		margin-right: 0.5rem;
		border: 1px solid #ccc;
		background-color: #fff;
		cursor: pointer;
	}
	.subject-tabs button.active {
		background-color: #ddd;
		border-bottom-color: #ddd;
	}
	main {
		flex-grow: 1;
		position: relative;
		background-color: #f8f8f8;
	}
	.node-info-popup {
		position: fixed;
		bottom: 10vh;
		left: 50%;
		transform: translateX(-50%);
		background-color: rgba(0, 0, 0, 0.7);
		color: white;
		padding: 10px 20px;
		border-radius: 5px;
		z-index: 10;
	}

	.loading-overlay,
	.minigame-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.6);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 20;
	}

	.loading-indicator {
		background-color: white;
		padding: 20px 40px;
		border-radius: 8px;
		font-size: 1.2em;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
	}

	.minigame-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.6);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 20;
	}

	.node-info-popup.error {
		background-color: rgba(180, 0, 0, 0.8);
		color: white;
	}
</style>
