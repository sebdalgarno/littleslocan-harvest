<script>
	import Bar from 'svelte-chartjs/src/Bar.svelte';

	export let year;
	export let year_min;
	export let data_total;
	export let palette;
	export let planned;

	let update_totals;
	let update_labels;
	let update_palette = [];

	let yeardiff;
	$: if (year) {
		yeardiff = year - year_min + 1;
	}

	$: update_totals = data_total.map(function (el) {
		return el.area;
	});

	$: planned_totals = planned.map(function (el) {
		return el.area;
	});

	$: update_labels = data_total.map(function (el) {
		return el.year;
	});

	$: update_palette = palette.slice(0, yeardiff).reverse();

	// let barchart;

	// $: if (typeof barchart === "object") {
	//   modifyData(barchart, update_totals, update_labels);
	//   modifyColor(barchart, update_palette);
	// }

	function modifyData(chart, data, labels) {
		chart.data.datasets[0].data = data;
		chart.data.labels = labels;
		chart.update();
	}

	function modifyColor(chart, palette) {
		chart.data.datasets[0].backgroundColor = palette;
		chart.update();
	}

	function numberWithCommas(x) {
		return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
	}

	let data;
	$: data = {
		labels: update_labels,
		datasets: [
			{
				label: 'Planned (Ha)',
				data: planned_totals,
				backgroundColor: '#E6007E',
				borderColor: '#1F2937'
			},
			{
				label: 'Harvested (Ha)',
				backgroundColor: update_palette,
				borderColor: '#1F2937',
				data: update_totals
			}
		]
	};

	let options = {
		legend: {
		    display: false
		 },
		maintainAspectRatio: true,
		responsive: true,
		animation: false,
		scales: {
			xAxes: [
				{
                    stacked: true,
					categoryPercentage: 1.0,
					barPercentage: 1.0,
					gridLines: {
						color: 'rgba(0, 0, 0, 0)'
					}
				}
			],
			yAxes: [
				{
                    stacked: true,
					ticks: {
						beginAtZero: true,
						callback: function (value, index, values) {
							if (parseInt(value) >= 1000) {
								return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
							} else {
								return value;
							}
						}
					},
					scaleLabel: {
						display: true,
						labelString: 'Harvested (Ha)'
					},
					gridLines: {
						color: 'rgba(0, 0, 0, 0)'
					}
				}
			]
		}
	};
</script>

<div class="px-10 py-5" style="">
	<Bar {data} {options} />
</div>
