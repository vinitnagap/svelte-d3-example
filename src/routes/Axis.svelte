<script>
	export let orientation;
	export let margin;
	export let width;
	export let height;
	export let scale;
	export let label = '';

	$: ticks = scale.bandwidth ? scale.domain() : scale.ticks();

	$: offset = scale.bandwidth ? scale.bandwidth() / 2 : 0;
</script>

{#if orientation == 'left'}
	<g transform="translate({margin.left})">
		{#each ticks as tick}
			<g transform="translate(0,{scale(tick) + offset})">
				<line x2={-6} stroke="black" />
				<text text-anchor="end" dominant-baseline="middle" fill="black" x={-10}>{tick}</text>
			</g>
		{/each}
	</g>
{:else}
	<g transform="translate(0,{height - margin.bottom})">
		{#each ticks as tick}
			<g transform="translate({scale(tick) + offset})">
				<line y2={6} stroke="black" />
				<text text-anchor="middle" dominant-baseline="hanging" fill="black" y={10}>{tick}</text>
			</g>
		{/each}
	</g>
{/if}
