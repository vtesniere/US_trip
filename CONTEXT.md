# Project Context — US West Road Trip

> Read this file at the start of every conversation about this project.
> Update it whenever new features are added, decisions are made, or the user gives feedback.

---

## What This Is

An interactive road trip planner for a Western USA camper van journey starting from **LAX**. Built as a single-file static HTML app, styled to match the Oaxaca trip format.

**Local path:** `/Users/vladimirtesniere/Dev/Claude/US_trip/`

---

## Trip Setup

- **Start:** LAX (fly in, pick up camper van on arrival)
- **Vehicle:** Camper van (sleep in it — no hotel costs)
- **Must-do:** Death Valley + Las Vegas (on all 3 itineraries)
- **Open options:** Arizona/New Mexico south, Utah/Colorado north, or Northern California loop

---

## Three Proposed Itineraries

| Route | Name | Days | Miles | Total Cost (4 ppl) | Per Person | End Point |
|-------|------|------|-------|--------------------|------------|-----------|
| **A** | Desert Southwest | 14 | 1,540 | $4,835 | $1,210 | Fly from ABQ |
| **B** | Utah & Rockies | 12 | 1,380 | $4,145 | $1,036 | Fly from DEN |
| **C** | Northern California Loop | 10 | 1,390 | $3,435 | $859 | Return to LAX |

### Route A — Desert Southwest
LAX → Death Valley → Las Vegas → Grand Canyon (South Rim) → Sedona → Tucson/Saguaro NP → White Sands NM → Santa Fe → fly from ABQ

### Route B — Utah & Rockies
LAX → Death Valley → Las Vegas → Zion NP → Bryce Canyon → Arches/Moab → Mesa Verde → Denver → fly from DEN

### Route C — Northern California Loop
LAX → Big Sur (PCH) → San Francisco → Napa Valley → Yosemite → Sequoia/Kings Canyon → Death Valley → Las Vegas → LAX (round trip)

---

## File Structure

```
US_trip/
├── index.html     ← entire app (single file, ~340 lines)
└── CONTEXT.md     ← this file
```

---

## index.html — Architecture

### Data Structures (top of `<script>`)

| Variable | Type | Description |
|----------|------|-------------|
| `COLORS` | Object | Route colors: A=orange, B=indigo, C=green |
| `itineraries` | Object | Keys A/B/C, each with stops[], segments[], costs{} |
| `itineraries[k].stops[]` | Array | Each: `{num, name, sub, days, lat, lng, desc, highlights[]}` |
| `itineraries[k].segments[]` | Array | Each: `{from, to, miles, km, time, road, notes}` |
| `itineraries[k].costs` | Object | `{vanRental, gas, food, camping, parks, misc, total, perPerson, note}` |
| `routeLayers` | Object | Leaflet polyline layer groups, keyed A/B/C |
| `markerLayers` | Object | Leaflet marker layer groups, keyed A/B/C |

### Key Functions

| Function | What it does |
|----------|-------------|
| `fetchOSRM(fromLat, fromLng, toLat, toLng)` | Calls OSRM API with 10s timeout, returns `[[lat,lng],…]` or null |
| `buildRoute(routeKey)` | Draws dashed placeholder lines immediately; fires OSRM fetches in background that replace placeholders as they resolve |
| `attachSegTooltip(pl, routeKey, segIdx)` | Binds hover tooltip to a polyline showing leg name, miles, time, road |
| `makeStopMarker(stop, color, routeKey, stopIdx)` | Creates numbered divIcon marker with popup |
| `highlightSegment(routeKey, segIdx)` | Dims all other legs, thickens selected leg, fits map to its two endpoints. Second click resets to full-route view. |
| `resetHighlight(routeKey)` | Restores all legs to default weight/opacity |
| `applyHighlight(routeKey, segIdx)` | Sets selected leg to weight=6/opacity=1, others to weight=3/opacity=0.18 |
| `syncSidebarSelection(stopIdx, segIdx)` | Marks correct stop card `.selected` and segment card `.seg-active`, scrolls into view |
| `renderSidebar(routeKey)` | Renders itinerary header, cost grid, stop cards + segment cards |
| `onStopClick(routeKey, stopIdx)` | Highlights outgoing leg from that stop |
| `onSegClick(routeKey, segIdx)` | Highlights that specific leg |
| `selectRoute(routeKey)` | Tab switch: hides other routes, renders sidebar, builds/shows current route, fits bounds |
| `fitRoute(routeKey)` | fitBounds to all stop coords with padding |

### Per-route storage

| Variable | Contents |
|----------|----------|
| `segLines[key]` | Array of `L.polyline`, one per driving leg (replaced by OSRM result when it arrives) |
| `stopMks[key]` | Array of `L.marker` with numbered divIcon |
| `mapGroup[key]` | `L.layerGroup` holding all layers for this route; added to map once |
| `built[key]` | `true` once `buildRoute` has been called (prevents re-fetching on tab re-visit) |

### Routing
- **Library:** Leaflet.js 1.9.4 (CDN)
- **Tiles:** CartoDB Dark or Light (`carto.com/basemaps`) — no API key needed; switched by `toggleMode()`
- **Road routing:** OSRM public API (real road network geometry, not straight/geodesic lines)
  - Two servers tried in order: `router.project-osrm.org` → `routing.openstreetmap.de/routed-car`
  - 14-second timeout per server using manual `AbortController` + `setTimeout` (not `AbortSignal.timeout`, which has patchy browser support)
  - OSRM returns `[lng, lat]` pairs → swapped to `[lat, lng]` for Leaflet
  - Placeholder dashed straight lines drawn synchronously on tab load; replaced leg-by-leg as OSRM resolves
  - Replacement always updates `group.removeLayer` / `group.addLayer` regardless of which tab is active — no stale-result bugs
  - Loading bar shows remaining leg count; disappears when all resolve

### Day / Night Mode
- Toggle button in header: `☀ Day Mode` / `🌙 Night Mode`
- `toggleMode()` swaps `tileLayer` (Dark ↔ Light CartoDB), flips `body.day-mode` class, updates popup/tooltip styles via `applyPopupStyles()`
- All sidebar, header, badge, card colours overridden via `body.day-mode` CSS selectors
- Default: night (dark) mode

### Route Colors
- Route A (Desert Southwest): `#f97316` (orange)
- Route B (Utah & Rockies): `#818cf8` (indigo/purple)
- Route C (Northern California): `#4ade80` (green)

### Mobile Layout
- Breakpoint: `max-width: 768px`
- Map stacks above sidebar (48dvh / 52dvh split)
- Uses `100dvh`

---

## Cost Assumptions

All estimates for **4 people** in a camper van:

| Item | Assumption |
|------|-----------|
| Van rental | ~$150/day (one vehicle for 4) |
| Gas | ~$0.18/mile (camper van, ~25 mpg at ~$4.50/gallon) |
| Food | ~$120/day for 4 (cooking in van + occasional restaurant) |
| Camping | ~$15/night avg (one site for the van — free BLM dispersed + paid campgrounds) |
| Parks Pass | $80 one-time (America the Beautiful — covers all occupants per vehicle) |
| Misc | $250–350 (activities, gear, incidentals) |

Flights home are **not included** (Routes A and B fly from ABQ/DEN).

---

## User Preferences & Decisions

- Camper van — sleeping in the vehicle (no hotel budget)
- Must include: Las Vegas + Death Valley
- Open to: Arizona/NM south, Utah/CO north, or Northern CA loop
- Formatting to match the viaje_oaxaca project style
- **Do NOT use hooks** unless explicitly requested
- Ask before taking any action not explicitly instructed

---

## Session Notes

| Date | Change |
|------|--------|
| 2026-03-29 | Initial HTML created: 3 itineraries, Leaflet map, OSRM routing, cost grid |
| 2026-04-04 | Routing rewrite: dashed placeholders → OSRM real roads per leg; numbered divIcon markers; segment highlight on click (thicken selected, dim others); fitBounds to leg endpoints; loading bar; double-click resets to full view |
| 2026-04-04 | OSRM fix: dual-server fallback (project-osrm.org → openstreetmap.de), manual AbortController timeout, removed currentRoute guard bug that dropped results on tab-switch; Day/Night mode toggle with tile swap + full CSS theme |
