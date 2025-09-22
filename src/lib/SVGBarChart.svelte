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
		[K in keyof Item]: Item[K] extends number ? string : Item[K];
	};

	let width = $state(400);
	let height = $state(4650);
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

	let yScale = $derived(
		d3
			.scaleBand()
			.domain(data.map((d) => d.country))
			.range([0, height])
			.padding(0.1)
	);

	let colorScale = $derived(
		d3
			.scaleOrdinal<string>()
			.domain(data.map((d) => d.region))
			.range(d3.schemeTableau10)
	);
</script>

<svg {width} {height}>
	{#each data as d (d.country)}
		<rect
			x="0"
			y={yScale(d.country)}
			width={xScale(d.health)}
			height={yScale.bandwidth()}
			fill={colorScale(d.region)}
		/>
		<text
			x={xScale(d.health) - 5}
			y={(yScale(d.country) ?? 0) + yScale.bandwidth() / 2}
			dy="0.35em"
			text-anchor="end"
			fill="white"
			font-size="12"
		>
			{d.country}: {d.health}
		</text>
	{/each}
</svg>

<style>
    text {
        font-family: sans-serif;
    }
</style>
