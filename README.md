# 🏥 Ghana Healthcare Accessibility Analysis

**How accessible is healthcare in Ghana — and who's being left behind?**

---

## The Story Behind This Project

In 2023, Ghana's mainstream media was filled with reports about the country's struggling healthcare infrastructure. But something bothered me — the people responsible for fixing this (Members of Parliament, regulatory bodies, health authorities) were never truly held accountable. Why? Because **there wasn't enough data to hold them to it.**

They'd say *"we're working on Project A"* or *"Project B is underway"* — vague promises for a country of 30+ million people. And I couldn't find a single comprehensive dataset showing where healthcare facilities actually are across Ghana, let alone how accessible they are to ordinary people.

So I decided to build one myself.

## The Data Hunt (3–6 Months)

Finding consolidated healthcare facility data for Ghana turned out to be painfully difficult. I searched everywhere — the Humanitarian Data Exchange, various newsrooms and open data platforms. The best I could find was around 3,000–4,000 facilities. For an entire country, that felt incomplete.

Then one day, I revisited the project and remembered the **Ghana Health Service** platform. I wrote Selenium code to extract their data, and within **15 minutes**, I had what months of searching couldn't give me: **10,337 healthcare facilities** distributed across all 16 regions of Ghana.

This was, by far, the richest dataset I had ever worked with for any analysis.

> **Note on data ethics:** Due to data privacy considerations, raw data details are not shared publicly. Appropriate clearance from the relevant body would be needed for full data access.

## What This Project Does

This analysis investigates healthcare accessibility across Ghana using multiple dimensions:

- **Geographic Access** — How far do people actually have to travel to reach a healthcare facility?
- **Coverage Ratios** — How many facilities serve each person, by district and region?
- **Facility Type Gaps** — Which areas only have basic CHPS compounds but lack hospitals or clinics?
- **Urban vs. Rural Divide** — How does access compare between urban and rural populations?
- **Ownership Patterns** — How are government, private, and faith-based (CHAG) facilities distributed?
- **Regional Inequality** — Which regions are systematically underserved?
- **Two-Step Floating Catchment Area (2SFCA)** — A gold-standard spatial accessibility method using road network data
- **Composite Accessibility Index** — A single score per district combining all metrics

The final output is an **interactive scrollytelling visualization** — inspired by [The Pudding](https://pudding.cool/) — that tells the story of healthcare accessibility in Ghana through data.

## Methodology

- **Data Collection:** Web scraping (Selenium) from the Ghana Health Service platform
- **Population Data:** Ghana 2021 Population and Housing Census (Ghana Statistical Service)
- **Geospatial Validation:** Facility coordinates validated against GADM region and district polygon boundaries
- **Spatial Analysis:** Haversine distance calculations, centroid-based accessibility metrics
- **Road Network Data:** OpenStreetMap for travel-time based accessibility (2SFCA)
- **Approach:** Agile methodology with iterative data cleaning, validation, and analysis

## Data Cleaning Highlights

The raw data required significant cleaning and validation work:

- **District name standardization** — 104 naming mismatches resolved between hospital data and census data using fuzzy matching and manual verification
- **Region label corrections** — 51 facilities still labeled with pre-2019 region names (before Ghana's 10→16 region restructuring) were updated using polygon validation
- **District label corrections** — 1,030 facilities with outdated district assignments were corrected through spatial joins against district boundary polygons
- **Coordinate validation** — Every facility's coordinates checked against official administrative boundary shapefiles; 8 facilities with incorrect coordinates were corrected using verified Google Maps data
- **GUAN District creation** — Approximated polygon boundary for the newly created (2021) Guan District, which was absent from all available boundary datasets
- **Distance recalculation** — All hospital-to-district-centroid distances recalculated after corrections, reducing maximum outlier distance from 301km to 99.6km

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core analysis language |
| Pandas | Data manipulation and cleaning |
| GeoPandas | Geospatial operations and polygon validation |
| Shapely | Geometry operations |
| Selenium | Web scraping |
| FuzzyWuzzy | District name matching |
| Matplotlib / Plotly | Visualization |
| OpenStreetMap | Road network data |
| Scrollama / D3.js | Interactive scrollytelling |

## Project Structure

```
├── data/
│   ├── raw/                    # Original scraped data (not shared)
│   ├── cleaned/                # Processed datasets
│   └── shapefiles/             # Administrative boundary files
├── notebooks/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_exploratory_analysis.ipynb
│   └── 03_accessibility_analysis.ipynb
├── visualizations/
│   └── scrollytelling/         # Interactive visualization
├── README.md
└── requirements.txt
```

## Key Findings

*Analysis in progress — findings will be updated as the project develops.*

## Transparency Note

> The background story in this README was originally narrated verbally and transcribed with the help of AI (ChatGPT for transcription, Claude for writing assistance). The analysis, data collection, validation decisions, and all technical work are entirely my own.

## Status

🟡 **In Progress** — Data cleaning and validation complete. Exploratory analysis and scrollytelling visualization underway.

## Author

**Princilla Abena Koranteng**
Data Scientist | Content Creator | [That Tech Girlie](https://www.linkedin.com/in/princilla-koranteng/)

---

*If you're a policymaker, researcher, journalist, or just someone who cares about healthcare in Ghana — this project is for you.*
