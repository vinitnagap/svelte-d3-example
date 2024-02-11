<script>
	export let colorFeature;
	export let color;
</script>

<div class="container">
	<!-- legend label -->
	<div>{colorFeature}</div>
	<div class="swatches">
		<!-- for each category in our scale -->
		{#each color.domain() as d}
			<div class="swatch">
				<!-- colored square -->
				<div class="square" style:background-color={color(d)} />
				<!-- category name -->
				<div>{d}</div>
			</div>
		{/each}
	</div>
</div>

<style>
	.swatches {
		display: flex;
		align-items: center;
		gap: 1em;
	}

	.swatch {
		display: flex;
		align-items: center;
		gap: 0.25em;
	}

	.square {
		width: 15px;
		height: 15px;
	}
</style>
