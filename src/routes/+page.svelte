<script lang="ts">
	import * as htmlToImage from 'html-to-image';

	let raw: string = $state('');

	let lines: string[] = $derived(() => {
		if (!raw) return [];

		const ls = raw.split('\n');
		for (let i = 1; i < ls.length - 1; i++) {
			const row = [];
			ls[i].split(/\s+/).forEach((word) => {
				while (word.length > 0) {
					let hyidx = word.indexOf('-');
					let eqidx = word.indexOf('=');
					let idx = -1;
					if (hyidx !== -1 && eqidx !== -1) {
						idx = Math.min(hyidx, eqidx);
					} else if (hyidx !== -1) {
						idx = hyidx;
					} else if (eqidx !== -1) {
						idx = eqidx;
					}

					if (idx === -1) break;
					row.push(word.slice(0, idx));
					row.push(word.slice(idx, idx + 1));
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

	async function downloadImage() {
		const node = document.querySelector('.preview') as HTMLElement;

		if (!node) return;

		try {
			const dataUrl = await htmlToImage.toPng(node, {
				cacheBust: true,
				backgroundColor: 'white'
			});
			const link = document.createElement('a');
			link.download = 'image.png';
			link.href = dataUrl;
			link.click();
		} catch (error) {
			console.error('Error generating image:', error);
		}
	}
</script>

<div class="container-container">
	<div class="container">
		<div
			class="preview"
			style="display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 8px;"
		>
			<table>
				{#each lines() as line, i}
					{#if i === 0 || i === lines().length - 1}
						<tr>
							<td colspan="999"><b>{line}</b></td>
						</tr>
					{:else}
						<tr>
							{#each line as word, j}
								<td
									class:hyphen={word[word.length - 1] === '-' ||
										word[word.length - 1] === '.' ||
										word[word.length - 1] === '=' ||
										line[j - 1] === '-' ||
										line[j - 1] === '.' ||
										line[j - 1] === '='}
									class:spaced-left={word[0] !== '-' &&
										word[0] !== '=' &&
										word[0] !== '.' &&
										line[j - 1] &&
										line[j - 1][line[j - 1].length - 1] !== '-' &&
										line[j - 1][line[j - 1].length - 1] !== '.' &&
										line[j - 1][line[j - 1].length - 1] !== '='}
									class:spaced-right={word[word.length - 1] !== '-' &&
										word[word.length - 1] !== '=' &&
										word[word.length - 1] !== '.' &&
										line[j + 1] &&
										line[j + 1][0] !== '-' &&
										line[j + 1][0] !== '=' &&
										line[j + 1][0] !== '.'}
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
			<div style="color: #888; font-size: 6px;">zasoque.org/glossing</div>
		</div>
	</div>
	<div class="container">
		<textarea class="raw" bind:value={raw}></textarea>
	</div>
	<div class="container">
		<button onclick={downloadImage}>Download as Image</button>
	</div>
</div>

<style>
	.container-container {
		display: flex;
		gap: 2em;
		height: 100vh;
		flex-direction: column;
		margin: 0 auto;
		justify-content: center;
	}

	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}

	table {
		border-collapse: collapse;
	}

	td {
		padding: 0;
	}

	.spaced-left:not(:first-child) {
		padding-left: 1em;
	}

	.spaced-right {
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

	.preview {
		padding: 1em;
		box-sizing: border-box;
	}
</style>
