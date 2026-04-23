<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<style>
		@import url('https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css');

		body {
			margin: 0;
			font-family: 'Pretendard', system-ui, sans-serif;
		}
	</style>
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
