<script>
	import './style.css';
	import * as d3 from 'd3';
	import PlayerList from './PlayerList.svelte';
	import Scatterplot from './Scatterplot.svelte';
	import BarChart from './BarChart.svelte';

	// data comes from the load function in +page.js
	export let data;

	// default features to visualize
	let xFeature = 'strikeout';
	let yFeature = 'hit';
	let colorFeature = 'all_star';

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
	<div class="header"></div>
	<div class="main">
		<PlayerList dataset={data.dataset} />
		<Scatterplot dataset={data.dataset} {xFeature} {yFeature} {colorFeature} {color} />
		<BarChart dataset={data.dataset} feature={colorFeature} {color} />
	</div>
</div>

<style>
	.container {
		/* set the font */
		font-family: system-ui, sans-serif;
		font-size: 16px;

		height: 100vh;
		width: 100vw;

		display: flex;
		flex-direction: column;
		gap: 2em;

		padding: 2em;
	}

	.main {
		/* use rest of vertical space not used by header */
		flex: 1;
		/* allowing main to shrink */
		min-height: 0;
		/* place children next to each other */
		display: flex;
		gap: 2em;
	}
</style>
