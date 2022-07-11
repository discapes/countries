<script>
  import mapboxgl from "mapbox-gl";
  import { onMount } from "svelte";

  mapboxgl.accessToken = "pk.eyJ1IjoibHVjYXN3b2oiLCJhIjoiY2l5Nmg4cWU1MDA0ejMzcDJtNHJmZzJkcyJ9.WhcEdTYQH6sSw2pm0RSP9Q";

  onMount(() => {
    const map = new mapboxgl.Map({
      container: "map", // container ID
      style: "mapbox://styles/mapbox/streets-v11", // style URL
      // center: [-74.5, 40], // starting position [lng, lat]
      // zoom: 9, // starting zoom
      projection: "globe", // display the map as a 3D globe
    });
    map.on("style.load", () => {
      map.setFog({}); // Set the default atmosphere style
    });
    map.on("load", function () {
      map.addSource("countries", {
        type: "geojson",
        data: "/custom.geo.json",
      });

      map.addLayer({
        id: "state-fills",
        type: "fill",
        source: "countries",
        layout: {},
        paint: {
          "fill-color": "#627BC1",
          "fill-opacity": 0.5,
        },
      });

      map.addLayer({
        id: "state-borders",
        type: "line",
        source: "countries",
        layout: {},
        paint: {
          "line-color": "#627BC1",
          "line-width": 2,
        },
      });

      map.addLayer({
        id: "state-fills-hover",
        type: "fill",
        source: "countries",
        layout: {},
        paint: {
          "fill-color": "#627BC1",
          "fill-opacity": 1,
        },
        filter: ["==", "name", ""],
      });

      // When the user moves their mouse over the page, we look for features
      // at the mouse position (e.point) and within the states layer (states-fill).
      // If a feature is found, then we'll update the filter in the state-fills-hover
      // layer to only show that state, thus making a hover effect.
      map.on("mousemove", function (e) {
        var features = map.queryRenderedFeatures(e.point, { layers: ["state-fills"] });
        if (features.length) {
          map.getCanvas().style.cursor = "pointer";
          map.setFilter("state-fills-hover", ["==", "name", features[0].properties.name]);
        } else {
          map.setFilter("state-fills-hover", ["==", "name", ""]);
          map.getCanvas().style.cursor = "";
        }
      });

      // Reset the state-fills-hover layer's filter when the mouse leaves the map
      map.on("mouseout", function () {
        map.getCanvas().style.cursor = "auto";
        map.setFilter("state-fills-hover", ["==", "name", ""]);
      });

      map.on("click", function (e) {
        var features = map.queryRenderedFeatures(e.point, { layers: ["state-fills"] });
        if (features.length) {
          window.location = "https://en.wikipedia.org/wiki/" + features[0].properties.name;
        }
      });
    });
  });
</script>

<main class="bg-gradient-to-r from-purple-100 to-pink-100 h-screen overflow-y-auto flex flex-col justify-center items-center p-10">
  <h1>Hello world!</h1>
  <div id="map" class="h-96 w-1/2" />
</main>

<style global lang="postcss">
  @tailwind base;
  @tailwind utilities;

  body {
    @apply font-sans;
    color: #333;
    margin: 0;
    box-sizing: border-box;
    font-family: "Segoe UI", Roboto, Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
  }

  img {
    height: 16rem;
    width: 16rem;
  }

  p {
    /* max-width: 14rem; */
    margin: 1rem auto;
    line-height: 1.35;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4rem;
    font-weight: 100;
    line-height: 1.1;
    margin: 2rem auto;
    /* max-width: 14rem; */
  }

  /* h1 {
    display: block;
    font-weight: bold;
    font-size: 2em;
    margin-block-start: 0.67em;
    margin-block-end: 0.67em;
  } */

  h2 {
    display: block;
    font-weight: bold;
    font-size: 1.5em;
    margin-block-start: 0.83em;
    margin-block-end: 0.83em;
  }

  h3 {
    display: block;
    font-weight: bold;
    font-size: 1.17em;
    /* margin-block-start: 1em; */
    /* margin-block-end: 1em; */
  }
  h4 {
    display: block;
    font-weight: bold;
    font-size: 1em;
    margin-block-start: 1.33em;
    margin-block-end: 1.33em;
  }
  h5 {
    display: block;
    font-weight: bold;
    font-size: 0.83em;
    margin-block-start: 1.67em;
    margin-block-end: 1.67em;
  }

  h6 {
    display: block;
    font-weight: bold;
    font-size: 0.67em;
    margin-block-start: 2.33em;
    margin-block-end: 2.33em;
  }

  a {
    color: blue;
  }
</style>
