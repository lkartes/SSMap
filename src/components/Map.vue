<template>
	<div id="map" />
</template>

<script>
import L from 'leaflet';
import icon from 'leaflet/dist/images/marker-icon.png';
import iconShadow from 'leaflet/dist/images/marker-shadow.png';
import map from 'lodash/map';
import pick from 'lodash/pick';
import pickBy from 'lodash/pickBy';
import capitalize from 'lodash/capitalize';

const defaultIcon = L.icon({
	iconUrl: icon,
	shadowUrl: iconShadow,
	iconAnchor: [12, 41],
	popupAnchor: [0, -41]
});
L.Marker.prototype.options.icon = defaultIcon;
export default {
	components: {},
	data() {
		return {
			map: null,
			data: [],
			points: L.featureGroup()
		};
	},
	watch: {
		data(newData) {
			newData.forEach(person => {
				const marker = L.marker(JSON.parse(person.location)).addTo(this.points);
				marker.bindPopup(this.createPopup(person));
			});
		}
	},
	async mounted() {
		this.map = L.map('map').setView([41.8781, -87.6298], 12);
		L.tileLayer(
			'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png',
			{
				maxZoom: 19
			}
		).addTo(this.map);
		this.points.addTo(this.map);
		this.data = await this.getData();
	},
	methods: {
		getData: () => {
			return fetch(
				'http://datacollection.streetsamaritans.org/get'
			).then(response => response.json());
		},
		createPopup(person) {
			let html = '';
			if (person.name) {
				html = html.concat(
					`<div style="font-size: medium">${person.name}</div>`
				);
			}
			const props = pick(person, [
				'age',
				'ethnicity',
				'gender',
				'kids',
				'work',
				'needs'
			]);
			const valid = pickBy(props, a => a);
			const elements = map(valid, (value, key) => {
				return `<div>${capitalize(key)}: ${capitalize(value)}</div>`;
			});
			html = html.concat(elements.join('')).replace(':', '&#58;');
			return html;
		}
	}
};
</script>

<style scoped lang="scss">
#map {
	height: 500px;
}
</style>
