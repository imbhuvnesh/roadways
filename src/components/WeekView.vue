<template>
	<div class="week-navigation flex justify-between items-center px-4 py-2 bg-gray-200 space-x-3">
		<button @click="prevWeek" class="text-sm font-medium text-gray-600 hover:text-gray-800">Previous Week</button>
		<!-- <p class="text-xl font-semibold text-gray-800">{{ currentWeek }}</p> -->
		<button @click="goToCurrentWeek" class="text-sm font-medium text-gray-600 hover:text-gray-800">Current Week</button>
		<button @click="nextWeek" class="text-sm font-medium text-gray-600 hover:text-gray-800">Next Week</button>
	</div>
	<div class="week-grid grid grid-cols-7 gap-2 px-4 py-4">
		<div
			v-for="day in days"
			:key="day.date"
			class="day text-center text-sm font-medium rounded-md py-2 px-4 hover:bg-gray-100 hover:text-black"
			@click="
				changeDay(day);
				isSelectedDay(day.normalFormatDate);
			"
			:class="[
				isDateToday(day.normalFormatDate) ? today : '',
				isSelectedDay(day.normalFormatDate) ? selectedDate : '',
			]">
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
			today: {
				[`bg-gray-600`]: true,
				[`text-white`]: true,
			},
			selectedDate: {
				[`bg-gray-100`]: true,
      },
      isSelected: false,
		};
	},
	computed: {
		days() {
			return this.$days();
		},
		currentWeek() {
			return this.$currentWeek();
		},
		// selectedDay() {
		// 	return this.$selectedDay();
		// },
	},

	methods: {
		isDateToday(givenDate) {
			return new Date().toDateString() === new Date(givenDate).toDateString();
		},
		isSelectedDay(givenDate) {
			if (this.$selectedDay)
				return new Date(this.$selectedDay.normalFormatDate).toDateString() === new Date(givenDate).toDateString();
		},
	},
};
</script>

<style scoped>
/* Add your custom Tailwind styles here */
</style>
