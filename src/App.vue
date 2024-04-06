<template>
	<div class="week-view flex flex-col items-center w-full h-full">
		<div class="header flex justify-between w-full px-4 py-2 bg-gray-200">
			<h1 class="text-xl font-bold text-gray-800">Roadbaze</h1>
			<p class="text-sm text-gray-600">{{ currentWeek }}</p>
		</div>
		<div class="station-dropdown flex items-center w-full px-4 py-2 mt-2">
			<label for="station" class="text-sm mr-2 text-gray-600">Station:</label>
			<StationSelection
				:stations="stations"
				:selectedStation="selectedStation"
				@update:selectedStation="handleStationChange"
				class="w-full" />
		</div>
		<WeekView
			:days="days"
			:currentWeek="currentWeek"
			:bookings="weekBookings"
			@previous-week="prevWeek"
			@next-week="nextWeek"
			@booking-click="showBookingDetails"
			class="grid grid-cols-7 gap-2 mt-4 px-4" />
		<BookingDetails v-if="bookingDetails" :details="bookingDetails" @close="hideBookingDetails" />
	</div>
</template>

<script>
import StationSelection from "./components/StationDropdown.vue";
import WeekView from "./components/WeekView.vue";
import BookingDetails from "./components/BookingDetail.vue";

import axios from "axios";

export default {
	components: {
		StationSelection,
		WeekView,
		BookingDetails,
	},
	data() {
		return {
			stations: [],
			selectedStation: null,
			selectedDay: null,
			weekBookings: {},
			bookingDetails: null,
			currentWeek: this.getWeekString(), // Current week string representation
			days: this.getWeekDays(), // Array of objects representing days
		};
	},
	provide() {
		return {
			$stations: () => this.stations,
			selectedStation: this.selectedStation,
			$selectedDay: () => this.selectedDay,
			changeStation: this.handleStationChange,
			$days: () => this.days,
			$currentWeek: () => this.currentWeek,
			prevWeek: this.prevWeek,
			nextWeek: this.nextWeek,
			goToCurrentWeek: this.goToCurrentWeek,
			changeDay: this.changeDay,
		};
	},
	methods: {
		async fetchStations() {
			try {
				const response = await axios.get("https://605c94c36d85de00170da8b4.mockapi.io/stations");
				this.stations = response.data;
			} catch (error) {
				console.error("Error fetching stations:", error);
			}
		},
		getStartDateOfWeek(date) {
			const day = date.getDay(); // 0 (Sunday) to 6 (Saturday)
			const startDate = new Date(date.setDate(date.getDate() - day));
			return startDate;
		},
		getWeekString(date) {
			const today = date || new Date();
			const day = today.getDay(); // 0 (Sunday) to 6 (Saturday)
			const startDate = new Date(today.setDate(today.getDate() - day));
			const endDate = new Date(startDate.getTime() + 6 * 24 * 60 * 60 * 1000);
			return `${startDate.toLocaleDateString()} - ${endDate.toLocaleDateString()}`;
		},
		getWeekDays(date) {
			const today = date || new Date();
			const startDate = this.getStartDateOfWeek(today);
			const days = [];
			for (let i = 0; i < 7; i++) {
				const date = new Date(startDate.getTime() + i * 24 * 60 * 60 * 1000);
				days.push({
					date: date.toLocaleDateString("en", { weekday: "short", day: "2-digit", month: "short" }),
					normalFormatDate: date.toISOString().split("T")[0],
				});
			}
			return days;
		},
		handleStationChange(newStation) {
			this.selectedStation = newStation;
			this.weekBookings = {}; // clear existing bookings when station changes
			// this.getWeekBookings(newStation);
		},
		changeDay(newDay) {
			console.log('newDay: ', newDay);
			this.selectedDay = newDay;
		},
		goToCurrentWeek() {
			const startDate = this.getStartDateOfWeek(new Date());
			this.updateWeek(startDate);
		},
		prevWeek() {
			const currentDate = this.days[0].normalFormatDate;
			const startDate = this.getStartDateOfWeek(new Date(currentDate));
			startDate.setDate(startDate.getDate() - 7);
			this.updateWeek(startDate);
		},
		nextWeek() {
			const currentDate = this.days[0].normalFormatDate;
			const startDate = this.getStartDateOfWeek(new Date(currentDate));
			startDate.setDate(startDate.getDate() + 7);
			this.updateWeek(startDate);
		},
		updateWeek(startDate) {
			this.currentWeek = this.getWeekString(startDate);
			this.days = this.getWeekDays(startDate);
			// Fetch events for the new week based on selected station (implementation needed)
		},
		// async getWeekBookings(stationId) {
		// 	const startDate = new Date(this.weekBookings?.[this.days[0]]?.[0]?.startDate || new Date());
		// 	startDate.setDate(startDate.getDate() - startDate.getDay()); // Set to beginning of the week
		// 	try {
		// 		const response = await fetch(`https://605c94c36d85de00170da8b4.mockapi.io/stations`);
		// 		console.log("response: ", response);
		// 		this.weekBookings = await response.json();
		// 		this.weekBookings = this.weekBookings.reduce((acc, booking) => {
		// 			const bookingDate = new Date(booking.startDate).getDay();
		// 			acc[this.days[bookingDate]] = acc[this.days[bookingDate]] || [];
		// 			acc[this.days[bookingDate]].push(booking);
		// 			return acc;
		// 		}, {});
		// 	} catch (error) {
		// 		console.error("Error fetching bookings:", error);
		// 	}
		// },
		showBookingDetails(booking) {
			this.bookingDetails = booking;
		},
		hideBookingDetails() {
			this.bookingDetails = null;
		},
	},
	async created() {
		await this.fetchStations();
	},
};
</script>

<style scoped>
@media (max-width: 768px) {
	.week-view {
		flex-direction: column;
	}

	.header,
	.station-dropdown {
		justify-content: center;
	}

	.station-dropdown label {
		display: block;
		margin-bottom: 4px;
	}

	.WeekView {
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
	}
}
</style>
