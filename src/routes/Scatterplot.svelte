<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';

	export let dataset;
	export let xFeature;
	export let yFeature;
	export let colorFeature;
	export let color;

	const margin = { top: 35, right: 20, bottom: 50, left: 60 };

	const width = 400;
	const height = 400;

	$: x = d3
		.scaleLinear()
		.domain(d3.extent(dataset, (d) => d[xFeature]))
		.range([margin.left, width - margin.right]);

	$: y = d3
		.scaleLinear()
		.domain(d3.extent(dataset, (d) => d[yFeature]))
		.range([height - margin.bottom, margin.top]);
</script>

<svg {width} {height}>
	<g>
		{#each dataset as d}
			<circle cx={x(d[xFeature])} cy={y(d[yFeature])} fill={color(d[colorFeature])} r={3} />
		{/each}
	</g>

	<Axis scale={x} orientation={'bottom'} {height} {margin} />
	<Axis scale={y} orientation={'left'} {height} {margin} />
</svg>
