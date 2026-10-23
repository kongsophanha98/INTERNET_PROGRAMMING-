# Week 4 Answers

1. **Index as :key.** Vue uses the key to decide which component instance belongs to which item. If the list is filtered, the cards shift position, so the instance at index 0 is reused for a different event. A card's local state, like an open details panel, then stays at that position and ends up on the wrong event. With `:key="e.id"` the state follows the event.

2. **Why `visible` is computed.** A computed is cached: it only recalculates when something it reads (`search`, `category`, `events`) changes. A function called from the template runs on every re-render, even for unrelated changes. When I type in the search box, `search` changes and `visible` recalculates once. The template then reuses that same result for the count and the cards.

3. **Why the card emits `book`.** First, props are read-only, so mutating `event.booked` in the child breaks one-way data flow and Vue warns about it. Second, the parent owns the data and the rules (is there a seat left, update `myBookings`, save to localStorage), so the logic lives in one place. This also keeps the card reusable and easy to test.

4. **`const { booked } = reactive(event); booked++`.** Destructuring copies the number into a plain local variable, which has no connection to the reactive proxy. `booked++` only changes that copy, so Vue never sees a change and nothing re-renders. The fix is `event.booked++` or `toRefs(event)`.

5. **Where `myBookings` lives.** It lives in `App.vue`, because App owns `events` and is the common parent of everything that needs the bookings. The header shows the count, and `onBook` changes both the event and the list. If it were in `EventCard` or `EventList`, the header couldn't reach it, and each card would have its own copy. Data goes down as props and changes go up as events.