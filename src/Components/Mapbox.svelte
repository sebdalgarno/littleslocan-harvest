<script>
  import { onMount, onDestroy } from "svelte";
  import { mapbox, key } from "../mapbox.js";
  import {
    tileset_logging,
    tileset_wshed,
    tileset_buffer1,
    tileset_buffer2,
    coordinates,
    mapbox_style,
  } from "../consts";

  export let year = 1945;
  export let map_palette;
  export let map_palette_planned;
  export let map_palette_single;
  export let map_alpha;
  export let bounds;
  export let single = true;
  export let buffer1 = false;
  export let buffer2 = false;

  $: if (typeof map !== "undefined" && done) {
    filterLoggedAreas(year, single);
    if(!buffer1){
      filterBuffer1()
    }
    if(!buffer2){
      filterBuffer2()
    }
  }

  let done = false;

  let container;
  let map;
  var hoveredStateId = null;

  let yearbuff;
  $: yearbuff = year + 1000

  function filterAccumulate() {
    map.setFilter("logged", ["<=", "year", year]);
    map.setFilter("buffer1", ["<=", "year", year]);
    map.setFilter("buffer2", ["<=", "year", year]);
  }

  function filterSingle() {
    map.setFilter("logged", ["==", "year", year]);
    map.setFilter("buffer1", ["==", "year", year]);
    map.setFilter("buffer2", ["==", "year", year]);
  }

  function filterBuffer1() {
    map.setFilter("buffer1", ["==", "year", yearbuff]);
  }

  function filterBuffer2() {
    map.setFilter("buffer2", ["==", "year", yearbuff]);
  }

  let paint_property = (year) => {
    return [
        "match",
        ["string", ["get", "planned"]],
        ...map_palette_planned,
        [
          "match",
          ["-", year, ["number", ["get", "year"]]],
          ...map_palette,
          map_palette[1],
        ],
      ]
  };

  let alpha_property = (year) => {
    return [
      "match",
          ["-", year, ["number", ["get", "year"]]],
          ...map_alpha,
          map_alpha[1],
        ]
  };

  function setPalette(year) {
    map.setPaintProperty("logged", "fill-color", paint_property(year));
    map.setPaintProperty("buffer1", "fill-color", paint_property(year));
    map.setPaintProperty("buffer2", "fill-color", paint_property(year));
  }

  function setPaletteSingle(year) {
    map.setPaintProperty("logged", "fill-color", map_palette_single);
    map.setPaintProperty("buffer1", "fill-color", map_palette_single);
    map.setPaintProperty("buffer2", "fill-color", map_palette_single);
  }

  function setAlpha(year) {
    map.setPaintProperty("buffer1", "fill-opacity", alpha_property(year));
    map.setPaintProperty("buffer2", "fill-opacity", alpha_property(year));
  }

  function filterLoggedAreas(year, single) {
    if(single){
      setPaletteSingle();
      filterSingle();
    } else {
      setPalette(year);
      filterAccumulate();
    }
  };

  onMount(async () => {
    map = new mapbox.Map({
      container,
      style: mapbox_style,
      center: coordinates,
      zoom: 5,
      bearing: 0,
      attributionControl: false,
      logoPosition: "bottom-right",
    });

    map.on("load", function () {
      map.addSource("wshed_line", {
        type: "vector",
        url: tileset_wshed,
      });
      map.addLayer({
        id: "wshed_line",
        source: "wshed_line",
        "source-layer": "wshed_line",
        type: "line",
        paint: {
          "line-width": 4,
          "line-color": "black",
        },
      });

      map.addSource("buffer1", {
        type: "vector",
        url: tileset_buffer1,
        promoteId: "id",
      });
      map.addLayer({
        id: "buffer1",
        source: "buffer1",
        "source-layer": "buffer1",
        type: "fill",
        filter: ["==", "year", year],
        paint: {
          "fill-opacity": 0.3
        },
      });

      map.addSource("buffer2", {
        type: "vector",
        url: tileset_buffer2,
        promoteId: "id",
      });
      map.addLayer({
        id: "buffer2",
        source: "buffer2",
        "source-layer": "buffer2",
        type: "fill",
        filter: ["==", "year", year],
        paint: {
          "fill-opacity": 0.3
        },
      });
 
      map.addSource("logged", {
        type: "vector",
        url: tileset_logging,
        promoteId: "id",
      });
      map.addLayer({
        id: "logged",
        source: "logged",
        "source-layer": "logging",
        type: "fill",
        filter: ["==", "year", year],
        paint: {
          "fill-opacity": [
            "case",
            ["boolean", ["feature-state", "hover"], false],
            0.5,
            1,
          ],
        },
      });
      map.addControl(new mapbox.AttributionControl(), "bottom-right");
      map.fitBounds(bounds);
      done = true;
    });

    var popup = new mapbox.Popup({
      closeButton: false,
      closeOnClick: false,
    });

    map.on("mousemove", "logged", function (e) {
      map.getCanvas().style.cursor = "pointer";

      var coordinates = e.lngLat;
      var description = `
      <p class='inline-block font-bold'> ${e.features[0].properties.description}</p>`;
      popup.setLngLat(coordinates).setHTML(description).addTo(map);
    });

    map.on("mousemove", "logged", function (e) {
      if (e.features.length > 0) {
        if (hoveredStateId) {
          map.setFeatureState(
            { source: "logged", id: hoveredStateId, sourceLayer: "logging" },
            { hover: false }
          );
        }
        hoveredStateId = e.features[0].id;
        map.setFeatureState(
          { source: "logged", id: hoveredStateId, sourceLayer: "logging" },
          { hover: true }
        );
      }
    });

    map.on("mouseleave", "logged", function () {
      map.getCanvas().style.cursor = "";
      popup.remove();
      if (hoveredStateId) {
        map.setFeatureState(
          { source: "logged", id: hoveredStateId, sourceLayer: "logging" },
          { hover: false }
        );
      }
      hoveredStateId = null;
    });    
  });
</script>

<style>
  .map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    z-index: 0.1;
  }

  .mapbox-popup {
    z-index: 100;
    max-width: 400px;
    font: 12px/20px "Helvetica Neue", Arial, Helvetica, sans-serif;
  }
</style>

<div class="map bg-black" bind:this={container}>
  <slot />
</div>
