<script>
	import Map from './Components/Mapbox.svelte';
	import Legend from './Components/Legend.svelte';
	import Button from './Components/Button.svelte';
	import Slider from 'svelte-range-slider-pips';
	import Chart from './Components/Barchart.svelte';
	import yearTotals from './year_totals.json'
	import planned from './planned.json';

	import Modal from "svelte-simple-modal";
  	import ModalAbout from "./Components/ModalAbout.svelte";
	import chroma from 'chroma-js';
	import { base_colors, coordinates, year_min, year_max, bounds } from './consts';

	let yeardiff = year_max - year_min;
	let palette = chroma.bezier(base_colors).scale().correctLightness().colors(60);

	for (let i = 60; i < yeardiff; i++) {
		palette.push(palette[59]);
	}

	let map_palette = [];
	let map_alpha = [];
	for (let i = 0; i < palette.length; i++) {
		map_palette.push(i);
		map_palette.push(palette[i]);
		map_alpha.push(i);
		map_alpha.push(0.3 - i/200)
	}

	let map_palette_planned = ['true', '#E6007E'];
	let map_palette_single = palette[10];
	let year = [1970];
	let single = false;
	let buffer1 = false;
	let buffer2 = false;

	let data_year;
	$: data_year = yearTotals.filter(function (x) {
		return x.year == year;
	});

	let data_total;
	$: data_total = yearTotals.filter(function (x) {
		return x.year <= year;
	});

	let totals;
	$: totals = data_total.map(function (el) {
		return Math.round(el.area, 1);
	});

	let logged_total;
	$: logged_total = numberWithCommas(sumValues(totals));

	let planned_total;
	planned_total = planned.filter(function (x) {
		return x.year == 2021;
	});

	let logged_year;
	$: if (data_year[0]) {
		logged_year = numberWithCommas(data_year[0].area);
	} else {
		logged_year = 0;
	}

	function toggleSingle() {
    single = !single;
  }

  function toggleBuffer1() {
    buffer1 = !buffer1;
  }

  function toggleBuffer2() {
    buffer2 = !buffer2;
  }

	function sumValues(numbers) {
		var x = numbers.reduce(function (prev, curr) {
			return curr + prev;
		}, 0);
		return x;
	}

	function numberWithCommas(x) {
		return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
	}

	function addYear() {
		if (year != year_max) {
			year[0]++;
		}
	}

	function minusYear() {
		if (year != year_min) {
			year[0]--;
		}
	}
</script>


<div class="grid grid-cols-5 bg-black">
	<div class="mt-5 col-span-2">
		<div class="grid-rows-3">
			<div class="row-span-1 text-center bg-black text-gray-300 px-8 mb-3">
				
				<span class="text-2xl text-gray-400">A history</span>
				<span class="text-xl text-gray-400">of</span>
				<span class="text-2xl text-gray-400">logging</span>
				<span class="text-xl text-gray-600">in</span>
				<span class="text-2xl text-gray-400">Little Slocan</span>			
			</div>
			

			<div class="row-span-1">
				<div class="w-full bg-opacity-75 p-2" style="z-index: 1;">
					<div class="text-center flex justify-center">
						<Button outline={false} caption={'-'} on:minus-year={minusYear} />
						<div class="inline-block">
							<p class="text-5xl text-gray-500">{year}</p>
						</div>
						<Button outline={false} caption={'+'} on:add-year={addYear} />
					</div>

					<div class="pl-20 w-10/12 -mt-2">
						<Slider bind:values={year} min={1945} max={2021} pips step={1} pipstep={10} />
					</div>

					{#if year == 2021}
						<div class="row-span-1 text-center bg-black text-gray-400 px-8">
							<span class="text-2xl text-green-700">{planned_total[0].area}</span>
							<span class="text-lg text-gray-700">hectares planned as of </span>
							<span class="text-2xl text-gray-500">{year}</span>
						</div>
					{:else}
						<div class="row-span-1 text-center bg-black text-gray-400 px-8">
							<span class="text-2xl text-green-700">{logged_year}</span>
							<span class="text-lg text-gray-700">hectares logged in</span>
							<span class="text-2xl text-gray-500">{year}</span>
						</div>
					{/if}

					<div class="row-span-1 text-center bg-black text-gray-400 px-8">
						<span class="text-2xl text-green-700">{logged_total}</span>
						<span class="text-lg text-gray-700">hectares logged from</span>
						<span class="text-2xl text-gray-500">1945</span>
						<span class="text-lg text-gray-700">-</span>
						<span class="text-2xl text-gray-500">{year}</span>
					</div>
					<div class="p-4">
						<Chart {year} {year_min} {data_total} {palette} />
					</div>
					<div>
						{#if !single}
						<Button caption={'by year'} on:single-year={toggleSingle} />
					  {:else}
						<Button caption={'all years'} on:single-year={toggleSingle} />
					  {/if}
					  {#if !buffer1}
					  <Button caption={'show small zone of influence'} on:buffer1={toggleBuffer1} />
					{:else}
					  <Button caption={'hide small zone of influence'} on:buffer1={toggleBuffer1} />
					{/if}
					{#if !buffer2}
					  <Button caption={'show large zone of influence'} on:buffer2={toggleBuffer2} />
					{:else}
					  <Button caption={'hide large zone of influence'} on:buffer2={toggleBuffer2} />
					{/if}
					</div>
					<Modal >
						<ModalAbout />
					  </Modal>
				</div>
			</div>
		</div>
	</div>

	<div class="col-span-3">
		<div
			class=" absolute ml-1 sm:my-1  p-0 md:p-2 rounded-lg bg-black bg-opacity-75 text-gray-400"
			style="z-index: 1; "
		>
			<Legend {palette} {map_palette_planned} />
		</div>
		<Map
			year={year[0]}
			{single}
			{buffer1}
			{buffer2}
			{map_palette}
			{map_palette_planned}
			{map_alpha}
			{map_palette_single}
			{bounds}
		/>
	</div>
</div>

<style>

</style>
