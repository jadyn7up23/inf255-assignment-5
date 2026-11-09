<script setup>
defineProps({
	activeFilter: { type: String, required: true },
});

const emit = defineEmits(["filter", "sort"]);

const filters = [
	{ value: "all", label: "All" },
	{ value: "available", label: "Available" },
	{ value: "adopted", label: "Adopted" },
];

function onSortChange(event) {
	emit("sort", event.target.value);
}
</script>

<template>
	<div class="controls">
		<div class="filters" role="group" aria-label="Filter animals">
			<button
				v-for="filter in filters"
				:key="filter.value"
				type="button"
				:class="{ active: activeFilter === filter.value }"
				:aria-pressed="activeFilter === filter.value"
				@click="emit('filter', filter.value)"
			>
				{{ filter.label }}
			</button>
		</div>

		<label for="sort-animals">Sort by:</label>
		<select id="sort-animals" @change="onSortChange">
			<option value="default">Original order</option>
			<option value="name">Name</option>
			<option value="age">Age</option>
		</select>
	</div>
</template>
