<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import { seatsLeft } from "./format";
import type { CampusEvent } from "./types";
const STORAGE_KEY = "campus-events-bookings";

// A reactive copy of the hard-coded events
const events = ref<CampusEvent[]>(initialEvents.map((e) => ({ ...e })));

// One entry per booked seat
const myBookings = ref<number[]>([]);
const bookingCount = computed(() => myBookings.value.length);

function onBook(id: number) {
  const event = events.value.find((e) => e.id === id);
  if (!event || seatsLeft(event) <= 0) return;
  event.booked++;
  myBookings.value.push(id);
}

// Restore bookings after a reload
function loadBookings() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    const saved: unknown = raw ? JSON.parse(raw) : [];
    if (!Array.isArray(saved)) return;
    for (const id of saved) {
      const event = events.value.find((e) => e.id === id);
      if (event && seatsLeft(event) > 0) {
        event.booked++;
        myBookings.value.push(id);
      }
    }
  } catch {
    // ignore corrupt or unavailable storage
  }
}
loadBookings();

watch(
  myBookings,
  (list) => {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(list));
    } catch {
      // ignore
    }
  },
  { deep: true },
);
</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>
      <p class="mine">
        You have booked {{ bookingCount }} {{ bookingCount === 1 ? "seat" : "seats" }}.
      </p>
    </header>
    <EventList :events="events" @book="onBook" />
  </main>
</template>
