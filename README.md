# US West Road Trip

An interactive road trip planner for a Western USA camper van journey starting from LAX. Three complete itinerary proposals with mapped routes, driving legs, and budget breakdowns for 4 travellers.

**Live site:** https://vtesniere.github.io/US_trip/

---

## The Trip

- **Start:** Los Angeles (LAX) — fly in, pick up a camper van
- **Vehicle:** Camper van (sleeping in the vehicle throughout)
- **Fixed stops:** Death Valley and Las Vegas appear on every route
- **Budget:** Estimates for 4 people, including van rental

---

## Three Routes

### A — Desert Southwest (14 days, ~1,540 miles)
LAX → Death Valley → Las Vegas → Grand Canyon South Rim → Sedona → Tucson/Saguaro NP → White Sands NM → Santa Fe → fly home from ABQ

**Estimated total:** ~$4,835 (~$1,210 per person)

### B — Utah & Rockies (12 days, ~1,380 miles)
LAX → Death Valley → Las Vegas → Zion NP → Bryce Canyon → Arches/Moab → Mesa Verde → Denver → fly home from DEN

**Estimated total:** ~$4,145 (~$1,036 per person)

### C — Northern California Loop (10 days, ~1,390 miles)
LAX → Big Sur (PCH) → San Francisco → Napa Valley → Yosemite → Sequoia/Kings Canyon → Death Valley → Las Vegas → back to LAX

**Estimated total:** ~$3,435 (~$859 per person)

---

## Budget Assumptions

| Item | Rate |
|------|------|
| Van rental | ~$150/day |
| Gas | ~$0.18/mile (camper van) |
| Food | ~$120/day for 4 |
| Camping | ~$15/night avg (mix of free BLM and paid sites) |
| Parks Pass | $80 one-time (America the Beautiful — covers all national parks) |

Flights home are not included for routes A and B.

---

## Features

- Real road-network routes via OSRM (dual-server fallback)
- Click any stop or driving leg to zoom the map to that segment
- Segment highlighting — selected leg thickens, others dim
- Day / Night map mode toggle
- Numbered stop markers with popups
- Cost breakdown grid per route
- Mobile responsive (map + sidebar split)

---

## Tech

Single-file static HTML app — no build step, no dependencies beyond CDN.

- [Leaflet.js](https://leafletjs.com/) 1.9.4 — map rendering
- [CartoDB Basemaps](https://carto.com/basemaps) — dark and light tiles (no API key)
- [OSRM](https://project-osrm.org/) — open-source road routing
