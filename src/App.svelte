<script>
	import { onMount } from "svelte";
	import {scaleLinear} from "d3-scale";

	export const myName = "Delyar Tabatabai";

	let data;
	let xScaleNew;
	let xScaleTicks = [];

	onMount(async () => {
		const fetched = await fetch("/static/census2000.json");
		let rawData = (await fetched.json()).demographic_groups;
		console.log(rawData);

		console.log("before filter", rawData.length)
		data = rawData.filter((record) => {
			return record.year == 2000 && record.sex == "F";
		});
		console.log("after filter", data.length)

		let arrayOfOnlyPopulation = data.map((record) => {
			return record.people
		})

		maximumPopulation = Math.max(...arrayOfOnlyPopulation);

		xScaleNew = scaleLinear()
			.domain([0, maximumPopulation])
			.range([0, maximumWidth]);

	 	xScaleTicks = xScaleNew.ticks(10);
		console.log(xScaleTicks)
	});

	let maximumPopulation = 9999999999;
	let maximumWidth = 400;

	//take population value and retun the width of the bar
	function xScale(value){
		return value/maximumPopulation * maximumWidth
	}
</script>

<main>
	<h1>In-Class Acitivity by {myName}!</h1>

	<svg id="visualization">
		<g>
			<line class="axis" x1="70" y1="250" x2="500" y2="250" style="stroke: brown; fill: none;"/>
			<line class="axis" x1="70" y1="5" x2="70" y2="250" style="stroke: blue; fill: none;"/>
		</g>
		
		{#if data !== undefined}
			{#each data as record, index}
				<g transform="translate(50, {index * 10})">
					<!-- <rect class="bar" x="20" y="0" width={record.people/ 80000} height="8" /> -->
					<rect class="bar" x="20" y="0" width={xScale(record.people)} height="8" />
					<text x="-30" y="6">{record.age}, {record.year}</text>
				</g>
			{/each}
			<!-- {#each xScaleTicks as tick}
				<g transform="translate(50, 100)">
					<text x= {50 + xScaleNew(tick)} y="40">{tick}</text>
					<line class="tick" x1={50+ xScaleNew(tick)} y1="40" x2={50 + xScaleNew(tick)} y2="45"/>
				</g>
			{/each} -->
		{/if}
	</svg>

</main>

<style>
	.tick {
		fill:saddlebrown
	}

	h1 {
		font-size: 1.5rem;
	}
	svg#visualization {
		width: 600px;
		height: 500px;
		fill: rgb(224, 10, 46)
		
	}
	text {
		font-size: 0.6rem;
	}
	.bar{
		fill:aquamarine;
	}

</style>
