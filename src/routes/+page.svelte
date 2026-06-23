<script>
	import ProjectWheel from '$lib/components/ProjectWheel.svelte';
</script>

<main class="w-full h-screen bg-zinc-950 text-white overflow-hidden select-none">
	<ProjectWheel />
</main>

<style>
	:global(html),
	:global(body) {
		margin: 0;
		width: 100%;
		min-height: 100%;
		background: #09090b;
	}

	:global(*) {
		box-sizing: border-box;
	}

	main {
		width: 100%;
		height: 100vh;
		overflow: hidden;
		background: #09090b;
		color: white;
		user-select: none;
	}
</style>
