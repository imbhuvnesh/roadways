<template>
	<div :class="[type === 'pickup' ? pickupClass : dropClass]" class="text-center p-4 rounded shadow">
		<slot />
	</div>
</template>

<script>
const type = Object.freeze({
	Pickup: "pickup",
	Drop: "drop-off",
});

export default {
	props: {
		type: type,
	},
	data() {
		return {
			pickupClass: {
				[`bg-green-200`]: true,
			},
			dropClass: {
				[`bg-blue-200`]: true,
			},
		};
	},
};
</script>

<style scoped>
/* Adjust these colors as needed */
.text-center {
	@apply text-gray-700;
}

.shadow {
	@apply shadow-md;
}
</style>
