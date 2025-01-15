<script lang="ts" generics="C extends Component, T">
	import {
		getContext,
		mount,
		onDestroy,
		onMount,
		unmount,
		type Component,
		type ComponentProps
	} from 'svelte';
	import type { MapContext } from '$lib/types.js';
	// import L from 'leaflet';
	const { map } = getContext<MapContext>('map');

	type Props<C extends Component> = {
		latLng: L.LatLngExpression;
		options?: L.MarkerOptions | undefined;
		popup?: {
			component: C;
			props?: ComponentProps<C> | undefined;
		};
	};

	let { latLng, options, popup }: Props<C> = $props();
	console.log(map);

	let marker: L.Marker | undefined = $state(undefined);

	onMount(() => {
		// if(!map) return;
		if (typeof window === 'undefined' || !map) return;
		import('leaflet').then((L) => {
			marker = L.marker<T>(latLng, options);
			marker.addTo(map);
			console.log(marker);

			if (popup) {
				let popupComponent: Record<string, any> | null;

				marker.bindPopup(() => {
					let container = L.DomUtil.create('div');
					popupComponent = mount(popup.component, {
						target: container,
						props: popup.props
					});
					return container;
				});

				marker.on('popupclose', () => {
					if (popupComponent) {
						let old = popupComponent;
						popupComponent = null;
						// Wait to destroy until after the fadeout completes.
						setTimeout(() => {
							unmount(old);
						}, 500);
					}
				});
			}
		});
	});

	onDestroy(() => {
		if (marker) {
			marker.remove();
		}
	});

	$effect(() => {
		if (marker) {
			marker.setLatLng(latLng);
		}
	});
</script>
