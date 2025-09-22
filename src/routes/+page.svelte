<script lang="ts">
	import HTMLBarChart from "$lib/HTMLBarChart.svelte";
	import SVGBarChart from "$lib/SVGBarChart.svelte";
	import FullSVGBarChart from "$lib/FullSVGBarChart.svelte";
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>

<h2>Charts</h2>
<h3>HTML Bar Chart</h3>
<HTMLBarChart/>

<h3>SVG Bar Chart</h3>
<SVGBarChart/>

<h3>Full SVG Bar Chart with Axes</h3>
<FullSVGBarChart/>

<style>
    * {
        font-family: sans-serif;
    }
</style>
