<script>
	import './style.css';
	import Scatterplot from './Scatterplot.svelte';
	import FeatureControls from './FeatureControls.svelte';
	import BarChart from './BarChart.svelte';
	import * as d3 from 'd3';

	// data comes from the load function in +page.js
	export let data;

	// default features to visualize
	let xFeature = 'strikeout';
	let yFeature = 'hit';
	let colorFeature = 'all_star';

	// indices of the brushed data points in the scatter plot
	let selectedIndices = [];

	// callback function to update selectedIndices when the scatterplot is brushed
	function onbrush(indices) {
		selectedIndices = indices;
	}

	// get the unique categories in the dataset sorted by count
	$: categories = d3
		.groupSort(
			data.dataset,
			(g) => g.length,
			(d) => d[colorFeature]
		)
		.reverse();

	$: color = d3.scaleOrdinal().domain(categories).range(d3.schemeTableau10);
</script>

<div class="container">
	<FeatureControls dataset={data.dataset} bind:xFeature bind:yFeature bind:colorFeature />
	<div class="plots">
		<Scatterplot dataset={data.dataset} {xFeature} {yFeature} {colorFeature} {color} {onbrush} />
		<BarChart dataset={data.dataset} feature={colorFeature} {selectedIndices} {color} />
	</div>
</div>

<style>
	.container {
		/* set the font */
		font-family: system-ui, sans-serif;
		font-size: 16px;
		/* make the div take up the entire screen */
		height: 100vh;
		width: 100vw;
		/* add 32px of padding around the div */
		padding: 2em;
		/* put the controls on top of the plots with 32px of space in between */
		display: flex;
		flex-direction: column;
		gap: 2em;
	}

	.plots {
		/* place the plots next to each other, vertically centered and 80px apart */
		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 5em;
	}
</style>
