<template>
	<div v-if="!selectedDay">Select the day to continue</div>
	<div v-else-if="!selectedStation">Select the station to continue</div>
	<div v-else-if="pickups.length === 0 && drops.length === 0">No pickups and drop offs today</div>
	<div class="flex w-max">
		<div id="pickup" class="flex-1 mr-4">
			<h3>Pickups</h3>
			<div class="items" v-for="pickup in pickups">
				<Card type="pickup"> {{pickup.customerName}} </Card>
			</div>
		</div>
		<div class="border-l border-gray-300"></div>
		<div is="drops" class="flex-1 ml-4">
			<h3>Drops</h3>
			<div class="items" v-for="drop in drops">
				<Card type="drop-off"> hello </Card>
			</div>
		</div>
	</div>
</template>

<script>
import Card from "./Card.vue";

export default {
	components: {
		Card,
	},
	inject: ["$selectedDay"],
	props: {
		selectedStation: Object,
	},
	data() {
		return {
			pickups: [],
			drops: [],
		};
	},
	computed: {
		selectedDay() {
			return this.$selectedDay();
		},
	},
	methods: {
		getBookings() {
			if (this.selectedStation) {
				const station = this.selectedStation;
				const bookings = station.bookings;
				const todaysPickups = bookings.map((booking) => {
					const exp =
						new Date(booking.startDate).toDateString() == new Date(this.selectedDay.normalFormatDate).toDateString();
					if (exp) {
						return booking;
					}
				});
				this.pickups = todaysPickups;
				console.log("todaysPickups: ", todaysPickups);
			}
		},
	},
	watch: {
		selectedStation: function () {
			this.getBookings();
		},
	},
};
</script>

