<script lang="ts">
	import * as d3 from 'd3';

	interface Item {
		income: number;
		health: number;
		population: number;
		region: string;
		country: string;
	}

	type RawItem = {
		[K in keyof Item]: string;
	};

	let width = $state(400);
	let data: Item[] = $state([]);

	$effect(() => {
		const loadData = async () => {
			try {
				data = await d3.csv(
					'https://vega.github.io/vega-datasets/data/gapminder-health-income.csv',
					(d: RawItem): Item => ({
						...d,
						income: +d.income,
						health: +d.health,
						population: +d.population
					})
				);
				console.log(data);
			} catch (error) {
				console.error('Failed to load data:', error);
			}
		};

		loadData();
	});

	let xScale = $derived(
		d3
			.scaleLinear()
			.domain([0, d3.max(data, (d) => d.health) ?? 0])
			.range([0, width])
	);

	let colorScale = $derived(
		d3
			.scaleOrdinal()
			.domain(data.map((d) => d.region))
			.range(d3.schemeTableau10)
	);
</script>

{#each data as d (d.country)}
	<div class="bar" style="width: {xScale(d.health)}px; background: {colorScale(d.region)}">
		{d.country}: {d.health}
	</div>
{/each}

<style>
	.bar {
		background: steelblue;
		padding: 3px;
		margin: 1px;
		text-align: right;
		color: white;
		width: 0px;
	}

	* {
        font-family: sans-serif;
    }
</style>
