<template>
	<div class="week-view flex flex-col items-center w-full h-full">
		<div class="header flex justify-between w-full px-4 py-2 bg-gray-200">
			<h1 class="text-xl font-bold text-gray-800">Roadbaze</h1>
			<p class="text-sm text-gray-600 font-semibold">{{ currentWeek }}</p>
		</div>
		<div class="station-dropdown flex items-center w-full px-4 py-2 mt-2">
			<label for="station" class="text-sm mr-2 text-gray-600">Station:</label>
			<StationSelection :stations="stations" :selectedStation="selectedStation" class="w-full" />
		</div>
		<WeekView
			:days="days"
			:currentWeek="currentWeek"
			:bookings="weekBookings"
			@previous-week="prevWeek"
			@next-week="nextWeek"
			class="grid grid-cols-7 gap-2 mt-4 px-4" />

		<BookingList :selectedStation="selectedStation" />
	</div>
</template>

<script>
import StationSelection from "./components/StationDropdown.vue";
import WeekView from "./components/WeekView.vue";

import axios from "axios";
import BookingList from "./components/BookingList.vue";

export default {
	components: {
		StationSelection,
		WeekView,
		BookingList,
	},
	data() {
		return {
			stations: [],
			earliestDay: null,
			selectedStation: null,
			selectedDay: null,
			weekBookings: {},
			currentWeek: null, // Current week string representation
			days: null, // Array of objects representing days
		};
	},
	computed: {
		defaultCurrentWeek() {
			return this.getWeekString(this.earliestDay);
		},
		defaultDays() {
			return this.getWeekDays(this.earliestDay);
		},
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
				await this.findEarliestDate(this.stations);
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
			const today = new Date(date) || new Date();
			const day = today.getDay(); // 0 (Sunday) to 6 (Saturday)
			const startDate = new Date(today.setDate(today.getDate() - day));
			const endDate = new Date(startDate.getTime() + 6 * 24 * 60 * 60 * 1000);
			this.currentWeek = `${startDate.toLocaleDateString()} - ${endDate.toLocaleDateString()}`;
			return this.currentWeek;
		},
		getWeekDays(date) {
			// const earliestDate = this.findEarliestDate();
			console.log(this.earliestDate);
			const today = new Date(date) || new Date();
			const startDate = this.getStartDateOfWeek(today);
			const days = [];
			for (let i = 0; i < 7; i++) {
				const date = new Date(startDate.getTime() + i * 24 * 60 * 60 * 1000);
				days.push({
					date: date.toLocaleDateString("en", { weekday: "short", day: "2-digit", month: "short" }),
					normalFormatDate: date.toISOString().split("T")[0],
				});
			}
			this.days = days;
			return days;
		},
		handleStationChange(newStation) {
			this.selectedStation = newStation;
		},
		changeDay(newDay) {
			console.log("newDay: ", newDay);
			this.selectedDay = newDay;
		},
		goToCurrentWeek() {
			const startDate = this.getStartDateOfWeek(new Date());
			this.updateWeek(startDate);
		},
		prevWeek() {
			const currentDate = this.days[0].normalFormatDate;
			console.log("currentDate: ", currentDate);
			const startDate = this.getStartDateOfWeek(new Date(currentDate));
			console.log("startDate: ", startDate);
			startDate.setDate(startDate.getDate() - 7);
			console.log("startDate: ", startDate);
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
		findEarliestDate(data) {
			let earliestDate = new Date(); // Initialize with current date/time

			data.forEach((city) => {
				city.bookings.forEach((booking) => {
					const startDate = new Date(booking.startDate);
					if (startDate < earliestDate) {
						earliestDate = startDate;
					}
				});
			});

			this.earliestDay = earliestDate?.toISOString().split("T")[0];
			return earliestDate?.toISOString().split("T")[0];
		},
	},
	async created() {
		await this.fetchStations();
		await this.findEarliestDate(this.stations);
		this.getWeekString(this.earliestDay);
		this.getWeekDays(this.earliestDay);
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
