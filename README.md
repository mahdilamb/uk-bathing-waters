# 🏖️ UK Bathing Waters

Find designated bathing sites in England, Wales and Scotland reachable within a chosen travel time — on foot, by bike or by train.

**[Live site →](https://mahdilamb.github.io/uk-bathing-waters/)**

![Screenshot of the app showing a map of southern England with bathing sites marked in green, blue, orange and red, and a results sidebar listing sites near Brighton](https://github.com/mahdilamb/uk-bathing-waters/raw/main/screenshot.png)

---

## Features

| | |
|---|---|
| **664 bathing sites** | England (EA), Wales (NRW), Scotland (SEPA) |
| **🚶 Walking** | Real road-routed times via [OSRM](https://router.project-osrm.org) |
| **🚲 Cycling** | Real road-routed times via [OSRM](https://router.project-osrm.org) |
| **🚂 Train** | Live journey times via [Huxley2](https://huxley2.azurewebsites.net) / National Rail, including walk to/from station |
| **Multi-mode** | Select any combination — sites reachable by *any* selected mode appear |
| **Quality filter** | Filter by Excellent / Good / Sufficient / Poor / Unknown |
| **Show all** | All sites visible on the map on load; search narrows by travel time |
| **Strava heatmap** | Swim heatmap overlay — tiles load if you're logged in to Strava |
| **Directions** | Every popup links to Google Maps directions |

## Data sources

| Nation | Source | Classifications |
|---|---|---|
| England | [Environment Agency](https://environment.data.gov.uk/doc/bathing-water.json) linked data API | Yes — annual compliance |
| Wales | [Natural Resources Wales](https://datamap.gov.wales) WFS | Not available |
| Scotland | [SEPA](https://map.sepa.org.uk/server/rest/services/Open/Environmental_Monitoring/MapServer/1) ArcGIS REST | Yes |

Station data (for train routing): [davwheat/uk-railway-stations](https://github.com/davwheat/uk-railway-stations) — 2,605 stations with CRS codes.

## How travel times work

### 🚶 Walking & 🚲 Cycling
Uses the [OSRM Table API](https://router.project-osrm.org) with the `foot` and `cycling` profiles. A crow-flies buffer (1.6× the estimated radius) pre-filters candidates before the routing call.

### 🚂 Train
1. Finds the nearest National Rail station to your location (within 3 km)
2. For each candidate bathing site, finds its nearest station
3. Calls [Huxley2](https://huxley2.azurewebsites.net) `/fastest/{from}/to/{dest}` to get the next fastest service
4. Fetches calling points to compute exact journey minutes
5. Adds estimated walking time each end (crow-flies × 1.4 ÷ 5 km/h)
6. Results are cached by station pair — changing the quality filter is instant

Falls back to a crow-flies estimate if no station is found nearby or Huxley2 returns no service.

## Keeping data fresh

A [GitHub Actions workflow](.github/workflows/refresh-data.yml) runs every Monday at 06:00 UTC. It re-fetches bathing water data from all three national APIs and opens a pull request if anything has changed, so you can review classification updates before merging.

A [deploy workflow](.github/workflows/deploy.yml) publishes the site to GitHub Pages on every push to `main`.

## Local development

The app is a single HTML file with no build step.

```bash
python3 -m http.server 3456
# then open http://localhost:3456
```

## Licence

Bathing water data © Environment Agency, Natural Resources Wales, SEPA — licensed under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).

Station data © [davwheat](https://github.com/davwheat/uk-railway-stations).

Map tiles © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors.

Train times via [Huxley2](https://github.com/jpsingleton/Huxley2) / National Rail Darwin.
