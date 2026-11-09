<script setup>
import dayjs from "dayjs";

defineProps({
	animal: { type: Object, required: true },
});

// App owns the animals, so this card reports the click and lets App update
// the status. If both components could change it, it would be harder to tell
// where a change came from or to keep the update rules in one place.
const emit = defineEmits(["toggle"]);

function arrivalText(intakeDate) {
	const date = dayjs(intakeDate).format("MMM D, YYYY");
	const daysAgo = dayjs().diff(intakeDate, "day");
	return `Arrived ${date} (${daysAgo} days ago)`;
}
</script>

<template>
	<article class="animal-card" :class="{ adopted: animal.adopted }">
		<h3>{{ animal.name }}</h3>
		<p>{{ animal.species }}, {{ animal.age }} years old</p>
		<p>{{ arrivalText(animal.intakeDate) }}</p>
		<p>{{ animal.adopted ? "Adopted" : "Available" }}</p>
		<button type="button" @click="emit('toggle')">
			{{ animal.adopted ? "Return" : "Adopt" }}
		</button>
	</article>
</template>
