<script setup lang="ts">
import { computed } from "vue";
import type { CampusEvent } from "../types";
import { formatDate, seatsLeft } from "../format";

const props = defineProps<{ event: CampusEvent }>();
const emit = defineEmits<{ book: [id: number] }>();

const left = computed(() => seatsLeft(props.event));
const isFull = computed(() => left.value <= 0);
const almostFull = computed(() => left.value >= 1 && left.value <= 5);
</script>

<template>
  <article class="card" :class="{ full: isFull }">
    <p class="category">{{ event.category }}</p>
    <h2>{{ event.title }}</h2>
    <p class="meta">{{ formatDate(event.startsAt) }} · {{ event.location }}</p>

    <p v-if="isFull" class="badge">Full</p>
    <p v-else class="seats" :class="{ warn: almostFull }">
      {{ left }} {{ left === 1 ? "seat" : "seats" }} left
    </p>

    <button :disabled="isFull" @click="emit('book', event.id)">Book a seat</button>
  </article>
</template>
