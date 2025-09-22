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

	let width = $state(600);
	let height = $state(4650);
	let margin = $state({ top: 40, right: 20, bottom: 20, left: 20 });
	let data: Item[] = $state([]);
	let sortByHealth = $state(false);

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
			.range([margin.left, width - margin.right])
	);

	let yScale = $derived(
		d3
			.scaleBand()
			.domain(
				sortByHealth
					? [...data].sort((a, b) => b.health - a.health).map((d) => d.country)
					: data.map((d) => d.country)
			)
			.range([margin.top, height - margin.bottom])
			.padding(0)
	);

	let colorScale = $derived(
		d3
			.scaleOrdinal<string>()
			.domain(data.map((d) => d.region))
			.range(d3.schemeTableau10)
	);

	let xAxis = $derived(d3.axisTop(xScale));

	let xAxisRef: SVGGElement;

	$effect(() => {
		if (xAxisRef && data.length > 0) {
			d3.select(xAxisRef).call(xAxis);
		}
	});
</script>

<div>
	<label>
		<input type="checkbox" bind:checked={sortByHealth} />
		Sort by health score (highest to lowest)
	</label>
</div>

<svg {width} {height}>
	{#each data as d (d.country)}
		<rect
			class="bar"
			x={margin.left}
			y={yScale(d.country)}
			width={xScale(d.health) - margin.left}
			height={yScale.bandwidth()}
			fill={colorScale(d.region)}
		/>
		<text
			class="label"
			x={xScale(d.health) - 5}
			y={(yScale(d.country) ?? 0) + yScale.bandwidth() / 2}
			dy="0.35em"
		>
			{d.country}: {d.health}
		</text>
	{/each}

	<g class="x-axis" transform="translate(0, {margin.top})" bind:this={xAxisRef}></g>

	<text
		x={width / 2}
		y={12}
		text-anchor="middle"
		class="axis-label"
	>
		Health Score
	</text>

</svg>

<style>
	svg {
		font-family: sans-serif;
	}

	.bar {
		stroke: white;
	}

	.label {
		fill: white;
		font-size: small;
		text-anchor: end;
	}

	.x-axis {
		color: #333;
	}

	.axis-label {
		font-weight: bold;
		fill: #333;
	}
</style>
