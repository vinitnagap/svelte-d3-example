<script>
	export let dataset;
</script>

<ul>
	{#each dataset as player}
		<li>{player.name} ({player.team})</li>
	{/each}
</ul>

<style>
	ul {
		width: 18em;
		/* make list no taller than parent */
		max-height: 100%;
		/* enable scrolling */
		overflow: auto;
		/* remove bullet point */
		list-style: none;
		/* remove extra spacing */
		padding: 0;
	}

	li + li {
		margin-top: 0.5em;
	}
</style>
