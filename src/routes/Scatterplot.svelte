<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';
	import ColorLegend from './ColorLegend.svelte';

	export let dataset;
	export let xFeature;
	export let yFeature;
	export let colorFeature;
	export let color;
	export let onbrush;

	// dimensions

	const width = 500;
	const height = 500;
	const margin = { top: 35, right: 20, bottom: 50, left: 60 };

	// scales

	$: x = d3
		.scaleLinear()
		.domain(d3.extent(dataset, (d) => d[xFeature]))
		.nice()
		.range([margin.left, width - margin.right]);

	$: y = d3
		.scaleLinear()
		.domain(d3.extent(dataset, (d) => d[yFeature]))
		.nice()
		.range([height - margin.bottom, margin.top]);

	// brushing
	// https://observablehq.com/@d3/brushable-scatterplot

	let brushGroup;

	function brushed(event) {
		if (event.selection) {
			const [[x1, y1], [x2, y2]] = event.selection;
			const indices = dataset
				// get the original indices of the data points
				.map((d, i) => [d, i])
				// filter to get the points in the brush
				.filter(([d, i]) => {
					const dx = x(d[xFeature]);
					const dy = y(d[yFeature]);
					return dx >= x1 && dx <= x2 && dy >= y1 && dy <= y2;
				})
				// get the indices
				.map(([d, i]) => i);
			onbrush(indices);
		} else {
			onbrush(d3.range(dataset.length));
		}
	}

	$: brush = d3
		.brush()
		.extent([
			[margin.left, margin.top],
			[width - margin.right, height - margin.bottom]
		])
		.on('start brush end', brushed);

	// reset the brush when the visualization changes
	$: xFeature, yFeature, d3.select(brushGroup).call(brush).call(brush.clear);

	// tooltip
	let tooltipData = null;
</script>

<div>
	<ColorLegend {color} {colorFeature} />

	<svg {height} {width}>
		<!-- group for the brush -->
		<g bind:this={brushGroup} />

		<!-- circles -->
		<g>
			{#each dataset as d}
				<circle
					cx={x(d[xFeature])}
					cy={y(d[yFeature])}
					fill={color(d[colorFeature])}
					r="3"
					role="graphics-symbol"
					on:mouseenter={(event) => (tooltipData = { d, x: event.clientX, y: event.clientY })}
					on:mouseleave={() => (tooltipData = null)}
				/>
			{/each}
		</g>

		<!-- axes -->
		<Axis orientation="bottom" scale={x} {width} {height} {margin} label={xFeature} />
		<Axis orientation="left" scale={y} {width} {height} {margin} label={yFeature} />
	</svg>

	{#if tooltipData}
		<div class="tooltip" style:left="{tooltipData.x}px" style:top="{tooltipData.y}px">
			{tooltipData.d.name}
		</div>
	{/if}
</div>

<style>
	div {
		display: flex;
		flex-direction: column;
		gap: 1em;
	}

	circle {
		transition:
			cx 250ms,
			cy 250ms;
	}

	.tooltip {
		padding: 0.25em 0.5em;
		position: absolute;
		background-color: white;
		border: 1px solid black;
		pointer-events: none;
	}
</style>
