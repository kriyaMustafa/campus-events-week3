<script setup lang="ts">
import { computed, ref } from "vue";
import type { CampusEvent, Category } from "../types";
import EventCard from "./EventCard.vue";

const props = defineProps<{ events: CampusEvent[] }>();
const emit = defineEmits<{ book: [id: number] }>();

const search = ref("");
const category = ref<Category | "all">("all");

const visible = computed(() => {
  const q = search.value.trim().toLowerCase();
  return props.events.filter((e) => {
    const matchesCategory = category.value === "all" || e.category === category.value;
    const matchesText =
      e.title.toLowerCase().includes(q) || e.location.toLowerCase().includes(q);
    return matchesCategory && matchesText;
  });
});
</script>

<template>
  <section>
    <div class="filters">
      <input v-model="search" type="search" placeholder="Search title or location" aria-label="Search" />
      <select v-model="category" aria-label="Category">
        <option value="all">All categories</option>
        <option value="talk">Talks</option>
        <option value="workshop">Workshops</option>
        <option value="social">Social</option>
        <option value="sport">Sport</option>
      </select>
    </div>

    <p class="count">{{ visible.length }} of {{ events.length }} events</p>

    <div v-if="visible.length" class="grid">
      <EventCard
        v-for="e in visible"
        :key="e.id"
        :event="e"
        @book="emit('book', $event)"
      />
    </div>
    <p v-else class="empty">No events match your search.</p>
  </section>
</template>
