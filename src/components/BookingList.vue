<template>
	<div class="mt-8 font-bold bg-gray-100 p-8" v-if="pickups?.length === 0 && drops?.length === 0">
		<h3>
			{{ !selectedStation || !selectedDay ? 'Please Select a station and day to start' : '' }}
			No pickups or returns on this
			day at {{ selectedStation.name }}
		</h3>
	</div>
	<div v-else class="flex w-max">
		<div id="pickup" class="flex-1 mr-4">
			<h3>Pickups</h3>
			<div class="items" v-for="pickup in pickups">
				<Card type="pickup" @click="showBookingDetails(pickup)">
					{{ pickup.customerName }}
				</Card>
			</div>
		</div>
		<div class="border-l border-gray-300"></div>
		<div is="drops" class="flex-1 ml-4">
			<h3>Drops</h3>
			<div class="items" v-for="drop in drops">
				<Card type="drop-off"> {{ drop.customerName }} </Card>
			</div>
		</div>
	</div>
</template>

<script>
import axios from "axios";
import Swal from "sweetalert2";

import Card from "./Card.vue";
import dayjs from "dayjs";

export default {
	components: {
		Card,
	},
	inject: ["$selectedDay", "$currentWeek"],
	props: {
		selectedStation: Object,
	},
	data() {
		return {
			pickups: null,
			drops: null,
		};
	},
	computed: {
		selectedDay() {
			return this.$selectedDay();
		},
		currentWeek() {
			return this.$currentWeek();
		},
	},
	methods: {
		async showBookingDetails(booking) {
			try {
				const stationId = booking.pickupReturnStationId;
				const bookingId = booking.id;
				const result = await axios.get(
					`https://605c94c36d85de00170da8b4.mockapi.io/stations/${stationId}/bookings/${bookingId}`
				);
				let { customerName, startDate, endDate } = result.data;
				const stationName = this.selectedStation.name;
				const duration = dayjs(endDate).diff(startDate, "day");
				startDate = new Date(startDate).toDateString();
				endDate = new Date(endDate).toDateString();
				const content = `<p>Customer Name - ${customerName}</p>
						<p>Station Name - ${stationName}</p>
						<p>Start Date - ${startDate}</p>
						<p>End Date - ${endDate}</p>
						<p>Duration - ${duration} days</p>
					`;
				Swal.fire({
					title: "Booking Details",
					html: content,
					icon: "info",
					confirmButtonText: "Cool",
				});
			} catch (error) {
				console.error(error);
			}
		},
		getBookings() {
			if (this.selectedStation) {
				const station = this.selectedStation;
				const bookings = station.bookings;

				if (this.selectedDay) {
					const todaysPickups = bookings.filter((booking) => {
						const bookingDate = new Date(booking.startDate).toDateString();
						const selectedDate = new Date(this.selectedDay.normalFormatDate).toDateString();
						return bookingDate === selectedDate;
					});
					const todaysDropoffs = bookings.filter((booking) => {
						const bookingDate = new Date(booking.endDate).toDateString();
						const selectedDate = new Date(this.selectedDay.normalFormatDate).toDateString();
						return bookingDate === selectedDate;
					});
					this.pickups = todaysPickups;
					this.drops = todaysDropoffs;
				} else {
					Swal.fire({
						icon: "info",
						text: "Select a station please",
						toast: true,
						position: "top-right",
						showClass: {
							popup: "animate__animated animate__bounceInRight",
						},
					});
				}
			} else {
				Swal.fire({
					icon: "info",
					text: "Select a station please",
					toast: true,
					position: "top-right",
					showClass: {
						popup: "animate__animated animate__bounceInRight",
					},
				});
			}
		},
	},
	watch: {
		selectedStation: function () {
			this.getBookings();
		},
		"selectedDay.normalFormatDate": function () {
			this.getBookings();
		},
		currentWeek: function () {
			this.pickups = [];
			this.drops = [];
		},
	},
};
</script>
