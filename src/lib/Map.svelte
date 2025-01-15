<script lang="ts">
	import { setContext, type Snippet } from 'svelte';
	import type { MapContext } from '$lib/types.js';

	type Props = {
		children?: Snippet;
		options: L.MapOptions;
		tileLayer?: L.TileLayer;
	};
	let { children, options, tileLayer }: Props = $props();

	let mounted = $state(false);

	let map: L.Map | undefined = $state(undefined);
	function mountMap(node: HTMLDivElement) {
		if (typeof window === 'undefined') return;
		import('leaflet').then((L) => {
			map = L.map(node, options);
			// setContext<MapContext>('map', {
			// 	map
			// })
			if (tileLayer) {
				tileLayer.addTo(map);
			} else {
				L.tileLayer('https://{s}.google.com/vt/lyrs=m&x={x}&y={y}&z={z}', {
					maxZoom: 20,
					subdomains: ['mt0', 'mt1', 'mt2', 'mt3']
				}).addTo(map);
			}
		});
	}

	$effect(() => {
		if (!map) return;
		setContext<MapContext>('map', {
			map
		});
		mounted = true;
	});
</script>

<svelte:window on:resize={() => map?.invalidateSize()} />
<svelte:head>
	<link
		rel="stylesheet"
		href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css"
		integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ=="
		crossorigin=""
	/>
</svelte:head>

<div style="width: 100%; height: 100%;" use:mountMap></div>

{#if map !== undefined && mounted}
	{@render children?.()}
{/if}
