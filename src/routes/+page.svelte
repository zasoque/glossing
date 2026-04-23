<script lang="ts">
	let raw: string = $state('');

	let lines: string[] = $derived(() => {
		if (!raw) return [];

		const ls = raw.split('\n');
		for (let i = 1; i < ls.length - 1; i++) {
			const row = [];
			ls[i].split(/\s+/).forEach((word) => {
				if (word.indexOf('-') !== -1) {
					const parts = word.split('-');
					parts.forEach((part, i) => {
						if (part) row.push((i === 0 ? '' : '-') + part);
					});
					return;
				}

				if (word) row.push(word);
			});
			ls[i] = row;
		}
		console.log(ls);
		return ls;
	});
</script>

<div class="container-container">
	<div class="container">
		<table>
			{#each lines() as line, i}
				{#if i === 0 || i === lines().length - 1}
					<tr>
						<td colspan="999"><b>{line}</b></td>
					</tr>
				{:else}
					<tr>
						{#each line as word}
							{#if word[0] === '-'}
								<td class="hyphen">{word}</td>
							{:else}
								<td class="spaced">{word}</td>
							{/if}
						{/each}
					</tr>
				{/if}
			{/each}
		</table>
	</div>
	<div class="container">
		<textarea class="raw" bind:value={raw}></textarea>
	</div>
</div>

<style>
	.container-container {
		display: flex;
		gap: 2em;
		height: 100vh;
		flex-direction: column;
		max-width: 800px;
		margin: 0 auto;
		justify-content: center;
	}

	.container {
		display: flex;
		justify-content: center;
		align-items: center;
	}

	table {
		border-collapse: collapse;
	}

	.spaced:not(:first-child) {
		padding-left: 1em;
	}

	.hyphen {
		padding-left: 0;
	}

	.raw {
		width: 100%;
		height: 200px;
	}
</style>
