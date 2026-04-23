<script lang="ts">
	let raw: string = $state('');

	let lines: string[] = $derived(() => {
		if (!raw) return [];

		const ls = raw.split('\n');
		for (let i = 1; i < ls.length - 1; i++) {
			const row = [];
			ls[i].split(/\s+/).forEach((word) => {
				while (word.length > 0) {
					console.log(word);
					let idx = word.indexOf('-');
					if (idx === -1) break;
					row.push(word.slice(0, idx + 1));
					word = word.slice(idx + 1);
				}
				if (word) row.push(word);
			});
			ls[i] = row;
		}
		return ls;
	});

	function caps(word: string) {
		return !/[a-z]/.test(word) && /[A-Z]/.test(word);
	}
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
							<td
								class:hyphen={word[word.length - 1] === '-' || word[word.length - 1] === '.'}
								class:spaced={word[word.length - 1] !== '-' && word[word.length - 1] !== '.'}
							>
								{#each word.split('.') as part, j}
									<span class:caps={caps(part)}>
										{part}{j === word.split('.').length - 1 ? '' : '.'}
									</span>
								{/each}
							</td>
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

	td {
		padding: 0;
	}

	.spaced:not(:first-child) {
		padding-right: 1em;
	}

	.hyphen {
		padding-left: 0;
	}

	.raw {
		width: 100%;
		height: 200px;
	}

	.caps {
		text-transform: lowercase;
		font-variant-caps: all-small-caps;
	}
</style>
