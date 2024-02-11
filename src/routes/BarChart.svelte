<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';

	export let dataset;
	export let feature;
	export let selectedIndices;
	export let color;

	// dimensions

	const width = 500;
	const height = 500;
	const margin = { top: 25, right: 20, bottom: 50, left: 60 };

	// filter the dataset by index
	$: filteredDataset = selectedIndices.map((i) => dataset[i]);

	// get the counts for the filtered dataset
	$: counts = d3.rollup(
		filteredDataset,
		(g) => g.length,
		(d) => d[feature]
	);

	// scales

	$: maxCount = d3.max(counts.values());

	$: x = d3
		.scaleLinear()
		.domain([0, maxCount])
		.nice()
		.range([margin.left, width - margin.right]);

	$: y = d3
		.scaleBand()
		.domain(color.domain())
		.range([margin.top, height - margin.bottom])
		.padding(0.1);
</script>

<div>
	<svg {height} {width}>
		<!-- bars -->
		<g>
			{#each counts as [category, count] (category)}
				<rect
					x={x(0)}
					y={y(category)}
					height={y.bandwidth()}
					width={x(count) - x(0)}
					fill={color(category)}
				/>
			{/each}
		</g>

		<!-- axes -->
		<Axis orientation="bottom" scale={x} {width} {height} {margin} label={'Count'} />
		<Axis orientation="left" scale={y} {width} {height} {margin} />
	</svg>
</div>

<style>
	rect {
		transition: width 250ms;
	}
</style>
