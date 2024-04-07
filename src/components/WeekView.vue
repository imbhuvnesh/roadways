<template>
	<div class="week-navigation flex justify-between items-center px-4 py-2 bg-gray-200 space-x-3">
		<button @click="prevWeek" class="week-btn">Previous Week</button>
		<!-- <p class="text-xl font-semibold text-gray-800">{{ currentWeek }}</p> -->
		<button @click="goToCurrentWeek" class="week-btn">Current Week</button>
		<button @click="nextWeek" class="week-btn">Next Week</button>
	</div>
	<div class="week-grid grid grid-cols-7 gap-2 px-4 py-4">
		<div
			v-for="day in days"
			:key="day.date"
			class="day "
			@click="
				changeDay(day);
				selected = day;
			"
			:class="[isDateToday(day.normalFormatDate) ? todayClass : '', isSelectedDay(day) ? selectedDateClass : '']">
			<span>{{ day.date }}</span>
		</div>
	</div>
</template>

<script>
export default {
	inject: ["$days", "$selectedDay", "$currentWeek", "prevWeek", "nextWeek", "goToCurrentWeek", "changeDay"],
	props: {
		bookings: Object,
	},
	data() {
		return {
			todayClass: {
				[`border`]: true,
				[`border-1`]: true,
				[`border-gray-300`]: true,
			},
			selectedDateClass: {
				[`bg-gray-100`]: true,
				[`border`]: true,
				[`border-gray-500`]: true,
			},
			selected: null,
		};
	},
	computed: {
		days() {
			return this.$days();
		},
		currentWeek() {
			return this.$currentWeek();
		},
	},

	methods: {
		isDateToday(givenDate) {
			return new Date().toDateString() === new Date(givenDate).toDateString();
		},
		isSelectedDay(givenDay) {
			return this.selected === givenDay;
		},
	},
};
</script>

<style scoped>
/* Add your custom Tailwind styles here */
</style>
