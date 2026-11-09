<script setup>
/*
 * Animal Adoption Board
 *
 * EXTRA CREDIT: If you recorded the video walkthrough (see README.md), paste
 * its link here. A video that is not linked here will not be graded.
 *
 *   Video link:
 */

import { ref, computed } from "vue";
import AnimalCard from "./components/AnimalCard.vue";
import AnimalControls from "./components/AnimalControls.vue";

// ------------------------------------
// App Data
// ------------------------------------

const animals = ref([]);
const loading = ref(false);
const error = ref("");

// ------------------------------------
// Load the Animals
// ------------------------------------

async function loadAnimals() {
	loading.value = true;
	error.value = "";

	try {
		const response = await fetch(`${import.meta.env.BASE_URL}animals.json`);
		if (!response.ok) {
			throw new Error(`Request failed with status ${response.status}`);
		}
		animals.value = await response.json();
	} catch (err) {
		console.error(err);
		error.value = "Sorry, the animals couldn't be loaded. Try refreshing the page.";
	} finally {
		loading.value = false;
	}
}

loadAnimals();

// ------------------------------------
// Adopt or Return an Animal
// ------------------------------------

function toggleAdoption(animal) {
	animal.adopted = !animal.adopted;
}

// ------------------------------------
// Filter, Sort, and Count
// ------------------------------------

const activeFilter = ref("all");
const activeSort = ref("default");

const visibleAnimals = computed(() => {
	let visible = animals.value.slice();

	if (activeFilter.value === "available") {
		visible = visible.filter((animal) => !animal.adopted);
	} else if (activeFilter.value === "adopted") {
		visible = visible.filter((animal) => animal.adopted);
	}

	if (activeSort.value === "name") {
		visible.sort((a, b) => a.name.localeCompare(b.name));
	} else if (activeSort.value === "age") {
		visible.sort((a, b) => a.age - b.age);
	}

	return visible;
});

const availableCount = computed(
	() => animals.value.filter((animal) => !animal.adopted).length,
);

function setFilter(filter) {
	activeFilter.value = filter;
}

function setSort(sort) {
	activeSort.value = sort;
}
</script>

<template>
	<header>
		<h1>Animal Adoption Board</h1>
		<p>Meet the animals currently staying at our shelter.</p>
	</header>

	<main>
		<section aria-labelledby="animals-heading">
			<h2 id="animals-heading">Animals</h2>

			<p v-if="!loading && !error" id="animal-count" aria-live="polite">
				{{ availableCount }} of {{ animals.length }} animals are available for adoption.
			</p>

			<AnimalControls
				:active-filter="activeFilter"
				@filter="setFilter"
				@sort="setSort"
			/>

			<div id="animal-list" aria-live="polite">
				<p v-if="loading">Loading animals...</p>
				<p v-else-if="error" class="error-message">{{ error }}</p>
				<p v-else-if="visibleAnimals.length === 0" class="empty-message">
					There are no animals to show.
				</p>
				<div v-else>
					<AnimalCard
						v-for="animal in visibleAnimals"
						:key="animal.id"
						:animal="animal"
						@toggle="toggleAdoption(animal)"
					/>
				</div>
			</div>
		</section>
	</main>
</template>
