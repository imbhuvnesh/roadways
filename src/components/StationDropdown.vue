<template>
	<div class="flex items-center">
		<label for="station" class="text-md text-white font-medium mr-2">Station:</label>
		<select
			id="station"
			v-model="chosenOption"
			@change="changeStation(chosenOption)"
			class="">
			<option value="" disabled hidden selected>Select a Station</option>
			<option v-for="station in stations" :key="station.id" :value="station">{{ station.name }}</option>
		</select>
	</div>
</template>

<script>
export default {
	inject: ["$stations", "changeStation"],
	data() {
		return {
			chosenOption: this.selectedStation,
		};
	},
	props: {
		selectedStation: Object
	},
	computed: {
		stations() {
			return this.$stations();
		},
	},
};
</script>

<style scoped>
/* Add your custom Tailwind styles here */
</style>
