<script lang="ts">
	import { onDestroy, setContext } from 'svelte';
	import mapboxgl from 'mapbox-gl';
	import 'mapbox-gl/dist/mapbox-gl.css'; 
	mapboxgl.accessToken = 'pk.eyJ1Ijoia2V2aW5sYWhvZGEiLCJhIjoiY2xuOWJucnEzMDVwcjJ2c2VtNWhuMTY4MCJ9.oDDjkq9KZ6BlMT2IlE6yig';
	const key = Symbol();
	export { mapboxgl, key };

	setContext(key, {
		getMap: () => map,
	});

	let map

	function initMap(container) {
		
		map = new mapboxgl.Map({
			container: container,
			style: 'mapbox://styles/kevinlahoda/cm1s76n2t001f01qq29v8avjv',
			center: [-93.929225,29.963658],
			zoom: 4
		});

		map.on('load', () => {

			map.addSource('facilities', {
	            type: 'geojson',
	            // Use a URL for the value for the `data` property.
	            data: '/geojson/2020-air-tox-fcilities-eto-top-150.geojson'
	        });

	        map.addLayer({
	            'id': 'facilities-layer',
	            'type': 'circle',
	            'source': 'facilities',
	            'paint': {
	                'circle-radius': [
	                	'interpolate',
	                	['linear'],
	                	['zoom'],
	                	10,
	                	['*', ['+', 2, ['to-number', ['get', 'Ethylene Oxide'], 4]], 1],
	                	13,
	                	['*', ['+', 2, ['to-number', ['get', 'Ethylene Oxide'], 4]], 10]
	                	],
	                'circle-stroke-width': 2,
	                'circle-color': '#1a1a1a',
	                'circle-stroke-color': 'white'
	            }
	        });

	        map.on('click', 'facilities-layer', (e) => {
	            map.flyTo({center: e.features[0].geometry.coordinates, zoom:12});
	        });

	        // Change the cursor to a pointer when the mouse is over the places layer.
	        map.on('mouseenter', 'facilities-layer', function () {
	            map.getCanvas().style.cursor = 'pointer';
	        });

	        // Change it back to a pointer when it leaves.
	        map.on('mouseleave', 'facilities-layer', function () {
	            map.getCanvas().style.cursor = 'default';
	        });

		});

	}


</script>

<div class="map-wrap">
  <div class="map" use:initMap></div>
</div>

<style>

.map {
  position: absolute;
  width: 100%;
  height: 100%;
}

</style>