**Feasibility: high, and low effort if you stay disciplined.** The app side is easy. The hard part is data, specifically whether a pump is actually open and has fuel.

**Where it beats Google Maps:** Maps shows stations, but it's weak on fuel-type filtering (CNG especially) and has no real-time "this CNG pump is down or has a queue" signal. That's your niche, and it matters most for CNG.

**Stack for speed**

- **Native Android: Kotlin + Jetpack Compose, with Glance for the widget.** Flutter and React Native are clumsy for home-screen widgets, and the widget is your core feature. Do iOS later.
- **Local-first design.** Ship the station dataset to the device (Room/SQLite). On widget tap: last known location → local nearest-station query filtered by the saved fuel type → fire a navigation intent (`google.navigation:q=lat,lng`) into Google Maps. No network, no custom navigation, well under a second.
- **Tiny backend:** Postgres + PostGIS, FastAPI or Go. It only syncs station data and serves open/closed status. Redis is optional.

**Data sources**

- **OpenStreetMap** (`amenity=fuel`, `fuel:cng=yes`, `fuel:diesel=yes`) is free and ingestible nightly. Coverage varies by region, so check your area first.
- **Google Places** is costly at scale and has restrictive caching terms. Use it sparingly, if at all.
- **Open/closed:** OSM `opening_hours` gives the baseline. For real status, add a one-tap "closed / no fuel / queue" report button, with reports expiring after a few hours. Taxi drivers are the ideal crowd for this, and it becomes your moat.

**Build order**

1. Widget + saved profile + local nearest query + Maps handoff.
2. Nightly OSM sync.
3. Crowd status reports.
4. Picture repair, only after the core is solid.

**Risks:** station data quality (especially CNG), cold-start for crowd reports, and widget behavior varying by phone brand. Test on Nothing OS early.