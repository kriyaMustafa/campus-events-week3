# Week 3 Practice: Vue Components and Reactivity - Answers

### 1. In EventList, what goes wrong if you use the array index as `:key` and the list gets filtered while a card has local state (for example an open details panel)?

If you use the array index as `:key`, Vue identifies DOM nodes and component instances by their positional index rather than the identity of the underlying event. When the list is filtered, items shift positions, causing Vue to reuse the existing component instance at that index along with its local state (such as an open details panel). Consequently, the local state remains attached to the index rather than the actual event item, causing an open panel to unexpectedly appear on a completely different event card.

---

### 2. Why is visible a computed and not a function called from the template? What is the difference when you type in the search box?

`visible` is defined as a `computed` property so that its calculation is cached and only re-evaluated when its reactive dependencies (`search`, `category`, or `props.events`) change. If it were a function invoked in the template, it would execute repeatedly on every single render cycle of the component, even when unrelated state updates occur. When typing in the search box, the `computed` property reactively updates and recalculates only as `search` changes, providing efficient memoization and optimal rendering performance.

---

### 3. EventCard emits `book` instead of changing `event.booked`. Give two reasons.

First, Vue adheres strictly to a one-way data flow where props are read-only; directly mutating `props.event.booked` inside a child component violates this contract and triggers Vue console warnings. Second, `App.vue` is the owner of the application state and needs to coordinate multiple pieces of data simultaneously. Emitting the `book` event lets `App.vue` validate available seats, update `event.booked`, and record the reservation in `myBookings` in a single coordinated source of truth.

---

### 4. You write `const { booked } = reactive(event); booked++`. The page does not change. Why?

Destructuring a reactive object extracts property values by value, which disconnects the primitive number from the reactive Proxy wrapper. Incrementing `booked++` merely modifies a local primitive JavaScript variable in memory rather than mutating the property on the reactive proxy itself. Because the proxy's setter is never triggered, Vue cannot detect the modification and therefore does not schedule a re-render of the page.

---

### 5. Where does myBookings live, and why not in EventCard or EventList?

`myBookings` lives in `App.vue` because state must be lifted to the lowest common ancestor shared by all components that depend on it. The header inside `App.vue` displays the total booking count, so the state must reside in `App.vue` to be accessible there. Furthermore, `myBookings` cannot live in `EventCard` because cards are destroyed when filtered and each card only represents a single event, nor in `EventList` because components outside the list need access to the booking count.
