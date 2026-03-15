Rest api. I have some routes which normally returns 200 OK. But I need a feature toggle to allow these routes be callable but if disabled to return another http code. Which one could be suitible?

---

Good question — the “right” status code depends a bit on **what you want to signal** to clients about the route when the feature is disabled. Here are the most common candidates:

* **`403 Forbidden`**
  → The client is *allowed* to call the route in principle, but the server refuses because the feature is not enabled.
  → This is the most common choice if the route exists but is currently off-limits.

* **`404 Not Found`**
  → Pretend the route doesn’t exist at all.
  → Useful if you don’t want clients to even know the feature exists when disabled.
  → Often used if the toggle is experimental, internal, or rollout-based.

* **`501 Not Implemented`**
  → Indicates the server doesn’t support the functionality (yet).
  → More fitting if the route is planned but not available in this environment.

* **`503 Service Unavailable`**
  → Tells the client “this service is temporarily unavailable, try again later.”
  → You can also include a `Retry-After` header.
  → Good if the feature is usually on, but can be temporarily switched off (like maintenance mode).

---

### Practical recommendation

* If you want to **hide** the feature when off → `404 Not Found`.
* If you want to **explicitly deny access** but keep route visible → `403 Forbidden`.
* If it’s a **temporary toggle / downtime** → `503 Service Unavailable`.

---

Do you want clients to be aware that the feature *exists but is disabled*, or should it be invisible when off? That will narrow it down.
