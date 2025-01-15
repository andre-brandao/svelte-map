# Svelte Maps

A small library made with [leaflet.js](https://leafletjs.com/)

## Exemple usage

```svelte
<script lang="ts">
	import Map from '$lib/Map.svelte';
	import Marker from '$lib/Marker.svelte';
	import Popup from './Popup.svelte';

	let markers: L.LatLngExpression[] = [
		[-19.96811611070914, -43.95033258481795],
		[-19.95811611070914, -43.97033258481795],
		[-19.97811611070914, -43.95033258681795],
		[-19.96815611070914, -43.97033258481795],
		[-19.96811611070914, -43.96043258481795],
		[-19.96811611070914, -43.96033258481795]
	];
</script>

<div>
	<Map options={{ center: [-19.96811611070914, -43.96033258481795], zoom: 13 }}>
		{#each markers as m}
			<Marker
				latLng={m}
				popup={{
					component: Popup
				}}
			/>
		{/each}
	</Map>
</div>

<style>
	div {
		width: 100%;
		height: 100vh;
	}
</style>
```

## Docs

### Map

```ts
type Props = {
	children?: Snippet;
	options: L.MapOptions;
	tileLayer?: L.TileLayer;
};
```

### Marker

```ts
type Props<C extends Component> = {
	latLng: L.LatLngExpression;
	options?: L.MarkerOptions | undefined;
	// popup can be any svelte component
	popup?: {
		component: C;
		props?: ComponentProps<C> | undefined;
	};
};
```

## Contribuiting

If you encounter any bugs, have feature requests, or would like to provide feedback, please feel free to open an issue or submit a pull request.
