<script>
  import { onMount } from "svelte";
  import Chart from "chart.js";

  export let year;
  export let year_min;
  export let data_total;
  export let palette;

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

  $: update_labels = data_total.map(function (el) {
    return el.year;
  });

  $: update_palette = palette.slice(0, yeardiff).reverse();

  let barchart;

  $: if (typeof barchart === "object") {
    modifyData(barchart, update_totals, update_labels);
    modifyColor(barchart, update_palette);
  }

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
    return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
  }

  onMount(async () => {
    let config = {
      type: "bar",
      data: {
        labels: update_labels,
        datasets: [
          {
            label: "Harvested (Ha)",
            backgroundColor: update_palette,
            borderColor: "#1F2937",
            data: update_totals,
          },
        ],
      },
      options: {
        scales: {
          xAxes: [
            {
              categoryPercentage: 1.0,
              barPercentage: 1.0,
              gridLines: {
                color: "rgba(0, 0, 0, 0)",
              },
            },
          ],
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
                callback: function (value, index, values) {
                  if (parseInt(value) >= 1000) {
                    return value
                      .toString()
                      .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
                  } else {
                    return value;
                  }
                },
              },
              scaleLabel: {
                display: true,
                labelString: "Harvested (Ha)",
              },
              gridLines: {
                color: "rgba(0, 0, 0, 0)",
              },
            },
          ],
        },
        legend: {
          display: false,
        },
      },
    };
    let ctx = document.getElementById("bar-chart").getContext("2d");
    barchart = new Chart(ctx, config);
  });
</script>

<canvas id="bar-chart" />
