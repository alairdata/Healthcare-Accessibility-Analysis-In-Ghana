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
| Jupyter Notebook | Development environment |
| python-Levenshtein | Speed optimization for fuzzy matching |
| math (haversine) | Distance calculations |
| GADM | Administrative boundary shapefiles |
| geoBoundaries | Alternative boundary data |


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

### Phase 0 — Project Scoping & Setup

### Defining the Project Vision
- Went through the project's six angles of analysis: geographic access, coverage ratios, facility type gaps, urban vs rural, ownership patterns, and regional inequality
- Decided on a **composite accessibility index** as the capstone deliverable — one score per district combining all metrics
- Chose **interactive scrollytelling** (Pudding.cool-style) as the final visualization format
- Decided on **both** a static report AND an interactive component for portfolio impact
- Agreed to work collaboratively — every step explained, nothing black-boxed

### Choosing the Tools
- Evaluated Python, R, Power BI, Tableau, and Excel
- Selected **Python (Jupyter Notebook)** as the core analysis tool for its flexibility with geospatial work, data manipulation, and visualization
- Excel was acknowledged as the starting point but insufficient for this scale of analysis

### Understanding the Data
- Loaded the Excel workbook containing **9 sheets** using `pd.read_excel()` with `sheet_name=None`
- Mapped out each sheet's purpose:
  - **raw data** — 10,337 scraped facilities (source of truth, don't touch)
  - **hospitals-data** — 9,978 cleaned facilities with lat/lon
  - **hospital-to-centroids-km** — facilities + distance from district centroid
  - **all-districts-data** — 2021 census with 834 rows (Main/Urban/Rural splits per district)
  - **gss-population-distribution** — 261 districts with urban/rural percentages
  - **district-centroids** — lat/lon for 261 district centers
  - **Table2, not-relevant-yet, analysis** — scratch work (ignored)

### Accessibility Methodology Discussion
- Discussed that distance-from-centroid measures distance from district center, not from where people actually live
- Explored the **Two-Step Floating Catchment Area (2SFCA)** method as the gold standard
- Decided to incorporate **OpenStreetMap road network data** for travel-time based accessibility
- Identified road data sources: Geofabrik, Humanitarian Data Exchange (HDX), WFP GeoNode (~159K km of roads in Ghana)

---

### Phase 1 — Data Preparation & Cleaning

### 1.1 Duplicate Check
- User challenged the assumption that duplicates existed in the data
- Defined duplication logic: same name + region + district + sub-district + community AND <1km apart
- Found 486 facilities sharing names with others
- After filtering by all location fields: only 4 rows (2 pairs)
- Both pairs were >1km apart (10.1km and 1.8km)
- **Conclusion: ZERO duplicates.** Different facilities legitimately share names in different locations

### 1.2 Missing Coordinates Check
- Verified all 9,978 facilities have valid lat/lon
- All coordinates fall within Ghana boundaries (4.5–11.5°N, -3.3–1.2°E)
- **Zero missing coordinate values**

### 1.3 District Name Standardization — The Big Challenge
- Initial match: only **157 of 261 districts** matched between hospital data and population data
- Problem: inconsistent naming conventions across datasets

**Examples of mismatches:**
- Hospital: "ABLEKUMA CENTRAL" → Population: "ABLEKUMA CENTRAL MUNICIPAL"
- Hospital: "ACCRA METRO" → Population: "ACCRA METROPOLITAN"
- Hospital: "KPONE-KATAMANSO" → Population: "KPONE KATAMANSO"

**Fuzzy matching attempt:**
- Used `fuzzywuzzy` library with `token_sort_ratio` scoring
- Got 157 exact matches + 104 needing review
- Many low-score matches were incorrect (e.g., KUMASI→EKUMFI at score 67)

**Manual district mapping:**
- Created a **104-entry translation dictionary** mapping hospital district names to population district names
- All mappings manually verified against the population dataset
- Key patterns: adding MUNICIPAL/METROPOLITAN suffixes, removing/adding hyphens, spelling corrections
- **Result: 261/261 districts now matched** via new `District_standardized` column

### 1.4 Demographics Data Flattening
- `all-districts-data` had Main/Urban/Rural as **separate rows** per district (834 total rows)
- Needed to flatten to one row per district

**Issues found:**
- 278 rows instead of expected 261
- 17 extra rows were **sub-metropolitan areas** (not separate districts):
  - Accra Metro subdivisions (3)
  - Kumasi Metro subdivisions (5)
  - Cape Coast Metro subdivisions (2)
  - Sekondi-Takoradi Metro subdivisions (3)
  - Tamale Metro subdivisions (2)
  - Tema subdivisions (3)
- Data quality issues: "Mion District District", "Mophor (Mpohor) Mpohor (Mpohor)"

**Fix:**
- Removed 17 sub-metro rows
- Fixed naming errors
- Result: 261 districts

**Initial flatten attempt failed** — Urban/Rural columns were all NaN because the merge used `Main District` column where Urban rows said "Jomoro Urban" instead of "Jomoro District"

**Fixed flatten** — merged on the `District` column instead, which was consistent across Main/Urban/Rural rows

**Final flattened output:** 29 demographic columns per district including Total/Male/Female × Main/Urban/Rural × Population/Household/NonHousehold

### 1.5 Master Dataset Merge
- Created a `make_simple_key()` function that strips hyphens, slashes, parentheses, suffixes (MUNICIPAL, METROPOLITAN, DISTRICT), and extra spaces
- Used `simple_key` to join across all datasets

**First merge attempt issues:**
- 2,303 facilities missing centroid data
- Urban/Rural gender columns showing NaN

**Centroid fix:**
- Found 27 districts with different names in centroid data (e.g., ADENTAN vs ADENTA, BOLGATANGA EAST vs BOLGA EAST)
- Created manual mapping for all 27
- Applied mapping and filled all missing centroids

**Result: Master dataset complete**
- 9,978 facilities × 31 columns
- Only missing values: rural data for 11 fully urban districts (100% urban, no rural data to fill) — correct behavior

### 1.6 Adding Distance from Centroid
- Added `distance_from_centroid_km` from the `hospital-to-centroids-km` sheet
- 1 facility missing (37 Military Hospital) — calculated using haversine formula
- Distance stats: mean 11.6km, median 9.4km, max 301.8km

---

### Phase 1.5 — Geospatial Validation (Day 2)

### 1.7 Investigating Distance Outliers
- Found 73 facilities more than 50km from their district centroid
- Top outlier: ABUADI CHPS at 301.8km (Adaklu, Volta → clearly wrong)
- Most were in large Savannah region districts (plausible) but top ones were suspicious

### 1.8 Region-Level Polygon Validation
- Downloaded **GADM Level 1 shapefile** (`gadm41_GHA_1.shp`) — confirmed 16 regions (2019 boundaries)
- Installed `geopandas` for geospatial operations
- Performed spatial join: checked which region polygon each facility's coordinates fall into
- **Results:**
  - 9,889 (99.1%) — coordinates in correct region
  - 85 (0.9%) — coordinates in wrong region
  - 4 — coordinates outside Ghana entirely

### 1.9 Categorizing Region Mismatches
Broke the 85 mismatches into categories:

**51 Region split cases (old map vs new map):**
- OTI → Volta (18 facilities)
- NORTHERN → Savannah (9)
- EASTERN → Bono East (6)
- And others following the 2019 region restructuring pattern
- **Fix:** Updated region labels to match polygon (trust the 2019 boundaries)

**27 Boundary cases:**
- Facilities near region borders where the polygon line puts them barely on the other side
- Small distances (6–25km), all in border districts
- Verified manually (e.g., Awutu Senya East is officially in Central region despite polygon edge)
- **Decision:** Left as is — stated region is correct

**7 Genuinely bad coordinates + 4 outside Ghana = 11 problem facilities:**
- I manually searched Google Maps for correct coordinates
- Found coordinates for **8 out of 11**
- 3 unfound: ABUADI CHPS, AWULAE BLAY IV CHPS, KEDZIKOPE CHPS

### 1.10 GUAN District Discovery
- GUAN district (created 2021, Oti Region) was missing from all available boundary datasets
- 17 facilities in the data tagged as GUAN district
- All 17 are in the SALL traditional areas: Santrokofi, Akpafu, Likpe, Lolobi
- Updated GUAN's region from VOLTA to OTI (correct per 2021 creation)

**Creating GUAN's polygon:**
- I decided to create the GUAN polygon rather than skipping
- Used convex hull around 17 facility coordinates + 2km buffer
- Cut the GUAN shape out of the Hohoe polygon
- Added GUAN as a new district to the shapefile
- **Result:** 261 districts in the shapefile, all 17 GUAN facilities correctly assigned

### 1.11 District-Level Polygon Validation
- Downloaded district-level boundary data — both GADM (`gadm41_GHA_2.shp`, 260 districts) and geoBoundaries (`geoBoundaries-GHA-ADM2.geojson`, 260 districts)
- Neither included GUAN — too new
- Used GADM + our created GUAN polygon = 261 districts

**District name matching (again):**
- `make_simple_key()` matched 237 of 261 automatically
- Manual mapping for remaining 24 (same patterns: hyphens, spelling, suffixes)
- GUAN mapped to Hohoe (its parent district in old boundary data)

**Spatial join results:**
- 8,946 (89.7%) — in correct district
- 1,030 (10.3%) — in wrong district
- 4 — outside all districts

### 1.12 Understanding the 1,030 District Mismatches
- Investigated the top mismatched pairs:
  - GA SOUTH → Weija Gbawe (44 facilities)
  - EAST GONJA → North East Gonja (33)
  - ACCRA METRO → Ablekuma West/North/Central (67 combined)
  - KUMASI → Kwadaso/Old Tafo/Suame (60 combined)

**Key insight:** This was the same pattern as the region splits — the Ghana Health Service platform **partially updated** district labels. Both old and new district names exist in the data (261 unique in both hospital data and polygon), but many facilities still had old labels.

**Deep dive — GA SOUTH example:**
- Hospital data: 56 facilities labeled GA SOUTH, 50 labeled WEIJA-GBAWE
- Polygon check: only 12 of the 56 GA SOUTH facilities are actually in the Ga South polygon
- 44 are physically in Weija Gbawe — the platform just didn't update their labels
- Confirmed the population data already uses the new district names

**Confirmed with the 11 "bad coordinate" facilities:**
- 7 of the 11 were in the 1,030 mismatches
- 4 were outside all districts
- User's Google Maps coordinates were actually CORRECT — the big distances were because facilities were compared to the wrong district's centroid
- Example: DENYASE CHPS — hospital data says Twifo Ati Morkwa, but Google search confirms it's in Upper Denkyira East Municipal (polygon was right)

### 1.13 District Centroid Fact Table
- Created a clean reference table: 261 districts + their centroids
- Added GUAN centroid manually (7°12'16.2"N, 0°31'8.8"E → 7.2045, 0.5191)
- Fixed MAMPONG → ASANTE MAMPONG naming mismatch

### 1.14 Updating All District Labels
- Updated 1,030 facilities to match polygon district names
- Standardized ALL district names to GADM naming convention
- Initial result: 301 unique districts (duplicates from mixed naming)
- Applied GADM name lookup to all 9,978 facilities
- **Final result: 261 unique districts, zero duplicates**

### 1.15 Recalculating All Distances
- Mapped every facility to its correct district centroid from the fact table
- Recalculated haversine distance for all 9,978 facilities

**Before vs After:**
| Metric | Before | After |
|--------|--------|-------|
| Mean distance | 11.6 km | 11.1 km |
| Max distance | 301.8 km | 99.7 km |
| Facilities >100km | Multiple | **ZERO** |
| Facilities >50km | 73 | 41 |

- The 41 facilities over 50km are all in large Savannah, Northern, Upper West districts — genuinely remote, not data errors
- The 3 "unfound" facilities (ABUADI, AWULAE BLAY IV, KEDZIKOPE) now show reasonable distances too — their coordinates were fine, it was the district labels that were wrong

### 1.16 Removing Unnecessary Flags
- Removed `bad_coordinates` column — no longer needed since all issues were resolved through district label corrections
- **Final master dataset: 9,978 facilities × 32 columns**

### 1.17 Saving the Master Dataset
- Saved as `master_dataset_v2.csv`

---

### Current Status

✅ **Phase 1 — COMPLETE**

The master dataset is clean, validated, and ready for analysis:
- **9,978 facilities** across **261 districts** in **16 regions**
- All coordinates validated against official administrative boundary polygons
- All district labels corrected to match 2019/2021 administrative boundaries
- All distances recalculated with correct district centroids
- Zero facilities over 100km from centroid
- Only missing values are rural data for 11 fully urban districts (correct behavior)

🔜 **Phase 2 — Exploratory Analysis** (Next)

🔜 **Phase 3 — Six Accessibility Analyses**

🔜 **Phase 4 — Composite Accessibility Index**

🔜 **Phase 5 — Scrollytelling Visualization**

---

## Key Decisions Made

1. **Zero duplicates** — user challenged assumption, analysis confirmed no true duplicates
2. **Trust polygon over hospital labels** — for both region and district assignments
3. **Create GUAN polygon manually** — rather than skipping the 17 facilities
4. **Update district labels** — 1,030 facilities corrected to match current administrative boundaries
5. **Keep all facilities** — nothing excluded, everything fixed
6. **2SFCA with road network** — chosen as gold standard accessibility method for later phases
7. **Scrollytelling** — chosen as final visualization format

---

## Data Quality Issues Found & Resolved

| Issue | Count | Resolution |
|-------|-------|------------|
| District name mismatches (hospital↔population) | 104 | Manual mapping dictionary |
| Centroid name mismatches | 27 | Manual mapping dictionary |
| Region labels using pre-2019 names | 51 | Updated to polygon region |
| District labels partially updated by platform | 1,030 | Updated to polygon district |
| Facilities with wrong coordinates | 8 | Corrected with Google Maps coordinates |
| GUAN district missing from boundary data | 1 district (17 facilities) | Created approximate polygon |
| Demographics flatten failure | All urban/rural rows | Fixed merge key (District vs Main District) |
| Sub-metropolitan rows in census data | 17 rows | Removed |
| Census naming errors | 2 ("Mion District District", "Mophor Mpohor") | Fixed |


## Transparency Note

> The project log in this README was originally narrated verbally and transcribed with the help of AI (ChatGPT for transcription, Claude for writing assistance). The analysis, data collection, validation decisions, and all technical work are entirely my own.

## Status

🟡 **In Progress** — Data cleaning and validation complete. Exploratory analysis and scrollytelling visualization underway.

## Author

**Princilla Abena Koranteng**
Data Scientist | Content Creator | [That Tech Girlie](https://www.linkedin.com/in/princilla-koranteng/)

*Last updated: March 1, 2026*

`#DataScience` `#HealthcareAnalysis` `#Ghana` `#GIS` `#GeoPandas` `#Python` `#ScrollyTelling` `#DataCleaning` `#SpatialAnalysis` `#PublicHealth` `#OpenData` `#2SFCA` `#PortfolioProject`


---

*If you're a policymaker, researcher, journalist, or just someone who cares about healthcare in Ghana — this project is for you.*
