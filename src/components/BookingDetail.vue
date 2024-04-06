<template>
	<div v-if="details" class="bg-white rounded shadow-md p-4">
		<h2>Booking Details</h2>
		<p class="mb-2"><span class="font-medium">Customer:</span> {{ details.customerName }}</p>
		<p class="mb-2"><span class="font-medium">Station:</span> {{ details.stationName }}</p>
		<p class="mb-2">
			<span class="font-medium">Dates:</span>
			{{ details.startDate }} - {{ details.endDate }}
		</p>
		<p class="mb-2">
			<span class="font-medium">Duration:</span>
			{{ details.duration }} days
		</p>
		<button
			@click="$emit('close')"
			class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
			Go Back
		</button>
	</div>
</template>

<script>
export default {
	props: {
		details: Object,
	},
};
</script>

<style scoped>
/* Add your custom Tailwind styles here */
</style>
