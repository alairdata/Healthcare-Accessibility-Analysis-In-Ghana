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
# Phase 2 — Exploratory Data Analysis

With the master dataset cleaned, validated, and ready (9,978 facilities across 261 districts and 16 regions), I began exploring the data to understand the landscape of healthcare infrastructure in Ghana before diving into the deeper accessibility modelling.

---

## Facility Type Distribution

The first question I asked was simple: what types of healthcare facilities do we actually have in Ghana?

The answer was immediately revealing. Out of 9,978 facilities, 6,732 (67%) are CHPS compounds — Community-based Health Planning and Services posts. These are the most basic tier of healthcare delivery in Ghana. They are staffed by community health nurses and can handle vaccinations, malaria treatment, family planning, basic wound care, and health education. They cannot perform surgery, handle complicated pregnancies, do blood transfusions, or treat serious conditions.

Beyond CHPS, there are 1,215 health centres, 929 clinics, 581 hospitals, 248 maternity homes, 145 district hospitals, 93 polyclinics, 10 teaching hospitals, 10 regional hospitals, 8 university hospitals, 5 psychiatric hospitals, and 2 leprosaria. I grouped these into three tiers for analysis: Basic (CHPS), Mid (Health Centre, Clinic, Maternity Home), and Advanced (Hospital, District Hospital, Polyclinic, Regional Hospital, Teaching Hospital, University Hospital, Psychiatric Hospital, Leprosarium).

The implication is significant: while Ghana appears to have nearly 10,000 healthcare facilities, the vast majority can only handle basic care. The country has just 10 regional hospitals and 10 teaching hospitals for a population of over 30 million people.

---

## Regional Overview

Next, I looked at how facilities are distributed across Ghana's 16 regions, combining facility counts with population data to calculate meaningful ratios.

Ashanti region has the highest number of facilities at 1,645, which makes sense as it is one of the most populated regions with 5.5 million people. Greater Accra follows with 1,464 facilities serving 6.2 million people. At the other end, North East region has the fewest facilities at just 138 for a population of 659,000.

However, raw facility counts are misleading because regions have vastly different populations. To compare fairly, I calculated the number of facilities per 10,000 people. This metric tells us: for every 10,000 people in a region, how many facilities are available to serve them? A higher number means better coverage.

Upper West region emerged as the best served with 5.69 facilities per 10,000 people, despite having relatively few total facilities (513). This is because its population is small (901,000). Central region turned out to be the worst served at just 2.08 per 10,000 people, followed closely by North East at 2.09. Greater Accra, despite having the second-highest number of facilities, only manages 2.35 per 10,000 due to its massive population.

I also examined the average distance from facilities to their district centroids as a rough proxy for how spread out the healthcare infrastructure is. Savannah region stands out dramatically with a 32km average distance — seven times further than Greater Accra's 4.5km. This means that even if Savannah had enough facilities per person, people would still struggle to physically reach them because of how spread out everything is.

The combined picture is sobering. North East gets hit on both sides — few facilities per person AND long distances. Savannah has somewhat reasonable ratios but terrible distances. Greater Accra has short distances but not enough facilities for its huge population. Upper West looks good on paper for ratios, but distances are still high at 15.7km.

---

## District-Level Analysis

Zooming in from regions to districts revealed even more extreme inequality.

The best-served district is North East Gonja in the Savannah region with 16.24 facilities per 10,000 people. The worst-served is GA South in Greater Accra with just 0.37 per 10,000 people. That is a 44-fold difference between the best and worst districts in the same country.

GA South is particularly alarming: it has a population of 350,000 people — larger than many regional capitals — and not a single hospital. If someone in GA South needs surgery or emergency care, they must travel to a neighbouring district.

---

## Facility Type Gaps

I then investigated which districts lack advanced healthcare facilities entirely — meaning they have no hospital, no district hospital, no polyclinic, nothing beyond basic and mid-level facilities.

The finding was stark: 38 districts, representing 15% of all districts in Ghana, have zero advanced healthcare facilities. People in these districts have no local access to surgery, emergency obstetric care, blood transfusions, or specialist treatment. For any serious medical need, they must travel to another district entirely.

These 38 districts are concentrated in specific regions. Savannah has 2 out of 7 districts (28.6%) without any hospital. Eastern has 8 out of 33 (24.2%). Oti, Western North, and Volta each have over 22% of their districts without hospitals.

Even Greater Accra, the capital region, is not exempt — GA South, one of the most populated districts in the country, has no hospital.

---

## Ownership Patterns

I examined who owns and operates Ghana's healthcare facilities, categorized as Government, Private, CHAG (Christian Health Association of Ghana), Quasi-Government, Other Faith-Based, and Mines.

Government facilities dominate at 7,972 (80% of all facilities). Private facilities account for 1,517 (15%), and CHAG contributes 365 (4%).

The regional distribution of private healthcare tells a clear story about investment patterns. Greater Accra has the highest proportion of private facilities at 35.6% — where there is money and population density, private healthcare fills gaps. Ashanti follows at 19.6% and Western at 16.5%.

At the other extreme, Upper West has only 2.7% private facilities, Savannah has 3.3%, and Oti has 4.0%. These are precisely the regions that are already worst served by public infrastructure. The implication is clear: the regions that need the most help are the ones where the private sector sees no commercial incentive to invest. If the government does not provide healthcare in these areas, nobody will.

---

## Urban vs Rural Comparison

Finally, I compared healthcare access between urban and rural areas by classifying districts as "Mostly Urban" (50% or more urban population) or "Mostly Rural" (less than 50% urban).

The results reveal a paradox. Rural areas actually have more facilities per person — 3.73 per 10,000 people compared to 2.83 in urban areas. This is because many CHPS compounds are deployed across rural communities.

However, the quality gap is significant. In rural areas, only 4.8% of facilities are in the Advanced tier (hospitals). In urban areas, 12.7% are Advanced. So rural Ghanaians are nearly three times less likely to have access to a hospital compared to their urban counterparts.

And when rural people do need to reach a facility, they travel nearly twice as far — 14.4km average distance compared to 7.6km in urban areas.

The summary: rural Ghana has more healthcare posts scattered around, but they are overwhelmingly basic-level facilities. When someone in a rural district needs real medical care — surgery, emergency treatment, specialist consultation — they face longer distances to reach a facility that probably does not exist in their own district.

# Phase 3 — E2SFCA Spatial Accessibility Analysis

The exploratory analysis in Phase 2 revealed significant inequality in healthcare distribution across Ghana. However, those findings relied on centroid-based distances — a rough measure that assumes people live at the centre of their district. To understand the true accessibility picture, I needed to model actual travel times using real road networks and real population locations. This is where the Enhanced Two-Step Floating Catchment Area (E2SFCA) method comes in.

---

## What is the E2SFCA and Why Does It Matter?

The E2SFCA is a gold-standard method in spatial epidemiology for measuring healthcare accessibility. Unlike simple distance metrics, it answers two questions simultaneously: can people physically reach a facility, and when they get there, will there be enough capacity to serve them?

The method works in two steps. In the first step, for every healthcare facility, I calculate how many people live within a reasonable travel time and compute a ratio — one facility divided by the weighted population it serves. A facility surrounded by a large population gets a low ratio (it is stretched thin), while a facility in a less populated area gets a higher ratio (it is less crowded).

In the second step, for every population point, I look at all the facilities reachable within the travel time cutoff and add up their ratios, weighted by distance. A population point that can reach many uncrowded facilities gets a high accessibility score. A point that can only reach one overcrowded facility, or none at all, gets a low or zero score.

The key advantage over centroid-based analysis is threefold. First, it uses real roads to calculate travel time, not straight-line distance. A facility might be 5km away as the crow flies but 20km by road because of terrain or missing infrastructure. Second, it accounts for competition — a hospital might be nearby but overwhelmed by the population it serves. Third, it uses population density data to model where people actually live, not just the district centre.

---

## The Three Input Datasets

The E2SFCA requires three datasets working together.

**Healthcare Facilities:** The master dataset of 9,978 facilities with validated coordinates, which I cleaned and validated in Phase 1.

**Population Density:** I downloaded a WorldPop population density raster for Ghana (`gha_pd_2020_1km_UNadj.tif`) at 1km resolution. This divides Ghana into a grid of 1km squares, each containing an estimate of how many people live there. I extracted 278,001 grid cells where people live, with a total WorldPop estimate of 36,950,968. Since the WorldPop data is from 2020 and overestimates compared to the 2021 Ghana census count of 30,832,019, I normalized the population using a scaling factor of 0.8344 so the spatial distribution comes from WorldPop but the total matches the census.

**Road Network:** I downloaded OpenStreetMap road data for Ghana from Geofabrik (`ghana-latest-free.shp.zip`), containing 373,884 road segments with 28 different road types.

---

## Road Network Analysis — A Critical Discovery

Before building the E2SFCA model, I investigated the road network to understand what types of roads exist across Ghana. This turned out to be one of the most important steps in the entire project.

I assigned each road segment to a district using spatial joins against GADM district polygons, then categorised all 28 road types into five groups: Major Roads (motorway, trunk, primary), Connecting Roads (secondary, tertiary), Urban/Town Roads (residential, service, living street), Rural/Unpaved (track, unclassified), and Walking/Non-vehicle (path, footway, pedestrian, steps, cycleway, bridleway).

The national breakdown showed that 79.1% of road segments are Urban/Town Roads, 10.9% are Rural/Unpaved, 6.7% are Walking/Non-vehicle paths, 2.1% are Connecting Roads, and 1.2% are Major Roads.

But the regional breakdown told a completely different story. In Oti region, 35.3% of all roads are walking paths. In Upper East, it is 28.5%. In Savannah, 21% are walking paths and another 39.8% are rural/unpaved — meaning roughly 60% of the road infrastructure in Savannah is either walking paths or dirt tracks. Compare this to Greater Accra where only 2.9% of roads are walking paths.

At the district level, the disparity is even more extreme. Tempane district in Upper East has 82.9% walking paths. Krachi Nchumuru in Oti has 70.3%. Zabzugu in Northern has 64.1%. East Gonja in Savannah has 63.0%.

This discovery had a direct impact on the modelling approach. An initial version of the analysis had excluded all non-driveable roads (paths, footways, steps), removing about 25,000 road segments. But this disproportionately cut off the very regions that are already the most underserved. In Tempane, removing walking paths would erase 82.9% of the road network, making it appear as if people there have almost no way to reach healthcare — when in reality, they do have paths, they just walk.

I decided to include all 28 road types in the analysis and assign appropriate speeds to each.

---

## Ghana-Corrected Speed Assignments

Standard international speed assumptions for road types are too generous for Ghana. A trunk road in the Savannah region does not allow 80 km/h travel — it passes through towns with speed bumps, police barriers, and heavy foot traffic. Link roads are bottleneck junctions, not cruising zones. Based on research into Ghana-specific road conditions, I assigned corrected speeds:

For major roads: motorway at 80 km/h (reduced from the typical 100-120), trunk at 60 km/h, primary at 45 km/h, and all link roads at 25 km/h (reduced significantly from typical assumptions, as these are transitional spaces).

For connecting roads: secondary at 40 km/h, tertiary at 30 km/h.

For urban roads: residential at 25 km/h, living street and service at 15 km/h.

For rural/unpaved: unclassified at 20 km/h, track at 15 km/h, graded tracks from 5-15 km/h depending on condition. These rural speeds were validated as realistic — many researchers make the mistake of assigning 40 km/h to laterite roads that are realistically impassable at those speeds, especially in the rainy season.

For walking paths: 4 km/h for paths, footways, and pedestrian areas, 3 km/h for steps. These are conservative upper limits — for a sick patient or someone carrying a child, actual speed would be lower.

Each road type was also assigned one of five transport mode labels for explainability: major_road, connecting_road, urban_road, rural_unpaved, or walking. This classification allows me to break down any journey into its component parts and say, for example, "56% of this journey is on foot."

---

## Building the Road Network Graph

With speeds and transport modes assigned, I built a network graph — a computational structure that represents all road connections in Ghana and allows the computer to calculate the fastest route between any two points.

Each road segment was broken into its constituent coordinate pairs, and for each pair, I calculated the real-world distance using latitude/longitude offsets with cosine correction for Ghana's position near the equator. I then converted distance to travel time using the assigned speed.

The resulting graph contained 4,374,664 nodes (intersection and waypoints) and 4,542,327 edges (road connections). Each edge stores two pieces of information: the travel time in minutes (used for routing) and the transport mode label (used for journey explainability).

I checked the connectivity of the network and found 1,180 disconnected components. The largest component contained 4,286,670 nodes, representing 98.0% of the entire network. The remaining 2% consisted of thousands of tiny disconnected fragments — driveways not snapped to the main road, walking paths in parks that do not touch any street, remote tracks that fade out before connecting to the main network. These are data artefacts in the OSM data, not real navigable infrastructure. I dropped them and retained only the largest connected component to ensure every calculation in the E2SFCA is based on a real, navigable path.

For computational speed, I converted the network from NetworkX (which was too slow for the scale of this analysis) to igraph. Testing confirmed that igraph could process 50 facilities simultaneously in about 124 seconds, bringing the estimated total computation time down to approximately 7 hours compared to the 79 days that a naive approach would have required.

---

## Snapping Facilities and Population Points to the Network

Before running the E2SFCA, I needed to connect every facility and every population grid cell to the road network. I built a spatial index (KD-Tree) over all 4.3 million road nodes, then for each of the 9,978 facilities and 278,001 population points, I found the nearest road node. This "snapping" step ensures that the model can calculate travel times from any population point to any facility via the road network.

---

## E2SFCA Parameters

**Cutoff: 120 minutes.** The standard in many studies is 60 minutes, but this is misleadingly tight for Ghana. Data from the Ghana Statistical Service and recent health surveys show that in regions like Oti, Savannah, and parts of the North, it is common for a significant portion of the population to travel more than two hours to reach a facility. The Lancet Commission on Global Surgery specifically recommends measuring population access within a 2-hour window for surgical and maternal emergency care. Using a 60-minute cutoff would effectively render these populations invisible in the analysis — showing them as having zero access when they are in fact seeking and receiving care, albeit with enormous difficulty. I extended the cutoff to 120 minutes to capture this rural reality.

**Decay function.** Rather than treating access as binary (inside 120 minutes = access, outside = no access), I used a distance decay function that weights facilities based on how far away they are. A facility 5 minutes away is far more useful than one 90 minutes away, and the decay function captures this:

- 0 to 15 minutes: weight 1.0 (immediate, optimal access)
- 16 to 30 minutes: weight 0.8 (good access, within Ghana Health Service target for CHPS)
- 31 to 60 minutes: weight 0.5 (moderate access, significant effort required)
- 61 to 120 minutes: weight 0.2 (marginalized access, common in rural Ghana)
- Over 120 minutes: weight 0.0 (no access)

This decay function was calibrated to reflect the Ghanaian context. The previous version used a sharper decay (0.1 for 30-60 minutes, 0.0 above 60 minutes) which unfairly penalised rural communities where 35-45 minute travel times are the norm, not an exception.

---

## Running the E2SFCA

The computation processed all 9,978 facilities in batches of 50, with progress tracking at every batch and automatic saves every 25 batches to protect against interruptions. The total computation took 14.9 hours.

For each batch, the model calculated the travel time from every facility in the batch to every node in the road network. It then identified which population points fall within the 120-minute catchment, applied the decay weights, and computed both the facility ratios (Step 1) and the population accessibility scores (Step 2).

---

## Results

The E2SFCA produced an accessibility score for each of the 278,001 population grid cells in Ghana.

**268,347 population points** (96.5%) have some level of access to healthcare — meaning at least one facility is reachable within 120 minutes via the road network.

**9,654 population points** (3.5%) have absolutely no access within 120 minutes. These are grid cells where people live but cannot reach any healthcare facility within two hours using any available road or path.

In terms of people, **30,621,336** (99.3% of the population) have some access, while **210,680 people** (0.7%) have no access at all. While 0.7% may sound small, these are over 200,000 real people — entire communities that are completely cut off from the healthcare system.

---

## Comparison with the Initial Run

I had previously run an earlier version of the E2SFCA using a 60-minute cutoff and only driveable roads (excluding walking paths). That version found 261,308 population points with access and 16,693 with no access.

The updated version — with all road types included and a 120-minute cutoff — found 268,347 with access and 9,654 with no access. By including walking paths and extending the catchment, approximately 7,000 additional population points were brought into the accessibility picture. These are communities that do have some access to healthcare, but only via footpaths or after travelling more than an hour. The earlier model had incorrectly classified them as having zero access.

---

Travel Time Analysis & Journey Breakdown

### Nearest Facility Travel Times — All 7 Facility Groupings

With the E2SFCA complete, the next step was to answer a more direct question: how long does it actually take each person in Ghana to reach the nearest healthcare facility — and how does that change depending on what type of care they need?

I defined seven facility groupings for this analysis:

| Grouping | Facilities | What it includes |
|----------|-----------|-----------------|
| Any | 9,978 | All facilities |
| CHPS | 6,733 | CHPS compounds only |
| Maternity | 6,981 | CHPS + maternity homes |
| Outpatient | 2,237 | Clinics + health centres + hospitals |
| Emergency | 726 | All hospitals |
| Specialist | 28 | Regional + teaching + university hospitals |
| Psychiatric | 5 | Psychiatric hospitals only |

For each of the 278,001 population grid cells, I calculated the travel time to the nearest facility in each grouping using a KD-Tree spatial index to identify the 10 nearest candidates by straight-line distance, then OSRM (Open Source Routing Machine) to compute exact road network travel times. OSRM was run locally via Docker using the latest Ghana OSM extract.

The computation used a two-stage approach: first, the KD-Tree identified the 10 nearest candidates to avoid routing to every facility in the country; then OSRM confirmed the true fastest route among those candidates via road. This reduced routing calls from billions to approximately 2.78 million while preserving accuracy.

Total computation time: approximately 4.5 hours across all 7 groupings. Zero failures across all 278,001 population points for all groupings.

---

### Key Findings — Travel Time by Facility Type

The results revealed a dramatic collapse in accessibility as the required level of care increases:

| Facility Type | Within 30 min | Within 60 min | Median travel time |
|---------------|---------------|---------------|--------------------|
| Any facility | 90.6% | 97.6% | 6.8 min |
| CHPS | 89.8% | 97.5% | 7.8 min |
| Maternity | 89.8% | 97.5% | 7.8 min |
| Outpatient | 72.6% | 91.9% | 17.3 min |
| Emergency | 42.1% | 74.6% | 35.3 min |
| Specialist | 6.6% | 20.9% | 109.4 min |
| Psychiatric | 0.9% | 3.5% | 265.5 min |

The headline statistic — that 90.6% of Ghanaians can reach a healthcare facility within 30 minutes — is technically accurate. But it is deeply misleading. That facility is almost always a CHPS compound: a single room, one community health worker, limited supplies, no doctor, no surgery, no emergency equipment.

The moment you need real care, the numbers collapse. Only 42.1% of Ghanaians can reach emergency care within 30 minutes. The median Ghanaian is 35 minutes from the nearest hospital — already past the internationally recognised golden hour for emergency intervention. For specialist care, the median travel time is 109 minutes. For psychiatric care, it is 265 minutes — over four hours. Only 0.9% of the population can reach a psychiatric hospital within 30 minutes.

This is the central finding of the project: Ghana's headline accessibility statistic conceals a catastrophic gap between primary care proximity and meaningful healthcare access.

---

### Worst Districts for Emergency Access

Five districts stand out as the most critically underserved for emergency care:

| District | Region | Avg emergency travel time | % within 30 min |
|----------|--------|--------------------------|-----------------|
| East Gonja | Savannah | 139 min | 0.0% |
| Kwahu Afram Plains South | Eastern | 129 min | 1.1% |
| North Gonja | Savannah | 108 min | 0.0% |
| Sekyere Afram Plains North | Ashanti | 95 min | 0.0% |
| Wa East | Upper West | 92 min | 2.2% |

East Gonja in the Savannah region is the most severe case. Not a single person in the entire district can reach emergency care within 30 minutes. The average journey to the nearest hospital takes 139 minutes — over two hours — through a road network that is 79.5% dirt tracks and unpaved roads.

---

### Journey Breakdown — Road Type Analysis

Knowing travel times tells us how long the journey takes. But it does not tell us what that journey is actually like. To answer this, I traced the route from every population point to its nearest facility and broke the journey down by road type.

Using the OSRM route API with geometry annotations, I retrieved the full route geometry for all 278,001 population-to-facility journeys. For each route, I matched every segment of the path to the nearest road in the OSM shapefile using a pre-built KD-Tree on road midpoints. This spatial matching allowed me to assign each segment of each journey to one of five road type categories: major road, connecting road, urban road, rural unpaved, or walking.

The total time attributed to each road type per journey was then summed to give a breakdown of how many minutes — and what percentage — of the average journey happens on each road type.

Total computation time: approximately 85 minutes for all 278,001 points. Zero routing failures.

---

### Key Findings — Journey Breakdown

| Road Type | Average time | % of journey |
|-----------|-------------|--------------|
| Rural unpaved | 14.3 min | 64.8% |
| Connecting road | 5.0 min | 22.8% |
| Major road | 2.2 min | 10.0% |
| Urban road | 0.5 min | 2.3% |
| Walking | ~0 min | ~0% |

Nearly two thirds of the average Ghanaian's journey to a healthcare facility — 64.8% — happens on dirt tracks and unpaved rural roads. These are roads where vehicles travel at 5 to 20 km/h. Roads that become impassable in the rainy season. Roads that no ambulance will travel down at night.

The road speed assignments used in this analysis reflect Ghana-specific conditions:

| Road category | Average speed |
|--------------|---------------|
| Major road | 45 km/h |
| Connecting road | 28 km/h |
| Urban road | 20 km/h |
| Rural unpaved | 11 km/h |
| Walking | 4 km/h |

At the district level, the variation in road quality is extreme. Ayawaso Central in Greater Accra spends 100% of its average journey on urban roads, with zero dirt road time. Akatsi North in the Volta Region spends 88.1% of its journey on dirt roads. Tatale Sanguli in Northern Region: 87.3%. These are not just numbers — they represent the physical reality of what it means to be sick in different parts of Ghana.

---

### Files Produced — Phase 3

| File | Description |
|------|-------------|
| `nearest_facility_times.csv` | Travel times to all 7 facility groupings for 278,001 population points |
| `journey_breakdown.csv` | Road type breakdown for all 278,001 population points |
| `master_population_data.csv` | Master file — 278,001 rows × 25 columns combining all Phase 3 outputs with district and region labels |
| `travel_time_summary.csv` | National summary statistics by facility grouping |

The master population dataset (`master_population_data.csv`) is the single most complete record produced by this project. Each row represents one 1km population grid cell and contains: coordinates, region and district assignment, population count, travel times to all 7 facility types, nearest facility name and type, journey breakdown by all 5 road categories, and the E2SFCA accessibility score.

---

# Phase 4 — Composite Accessibility Index

With all the computational phases complete, the final analytical step was to synthesise everything into a single, transparent, and defensible accessibility score for each of Ghana's 260 districts and 16 regions.

The challenge with composite scoring is that it is easy to build an index that looks rigorous but masks its assumptions. I wanted every weight, every threshold, and every methodological decision to be openly documented — so that a policymaker who disagrees with a weighting can understand exactly what they are disagreeing with and why.

The framework went through multiple iterations, including a second-opinion review with another AI model (Gemini), before arriving at the final design described here.

---

## The Two-Score Framework

The most important methodological decision was to separate the composite score into two fundamentally different sub-scores rather than collapsing everything into one number.

Healthcare accessibility has two distinct barriers:

**The mobility barrier** — Can people physically get to a facility? This is about travel time and road conditions.

**The capacity barrier** — When people arrive, is there enough to serve them? This is about supply relative to demand.

These two things are not the same. A district could have excellent road access to a nearby hospital that is completely overwhelmed with patients. A different district could have poor roads to a well-resourced facility with surplus capacity. Both have an accessibility problem, but the nature of the problem — and the policy solution — is completely different.

Mixing these two dimensions into a single weighted average would obscure which problem a district is actually facing. So I kept them separate.

---

## Score 1 — Journey Access Score (0–100)

**What it answers:** *How physically accessible is healthcare for people in this district?*

The Journey Access Score is built entirely from travel time data. It does not include road quality as a separate component — because road conditions are already embedded in the travel times themselves. OSRM assigns slow speeds to dirt tracks and walking paths, which means a journey on unpaved roads already takes longer and therefore falls into a worse time band. Including road quality separately would be double-counting the same barrier.

The score has three components:

**Emergency access — 50% weight**

Emergency care is life or death. Missing the 30-minute golden hour for trauma, stroke, or obstetric emergency has direct mortality consequences. Emergency access carries the largest weight in the score.

Time bands:

| Band | Score |
|------|-------|
| Within 30 min | 100 |
| 30 to 60 min | 70 |
| 60 to 90 min | 40 |
| 90 to 120 min | 15 |
| 120+ min | 0 |

**Specialist access — 35% weight**

Specialist care — regional hospitals, teaching hospitals, university hospitals — captures the quality gap. Chronic disease management, surgery, maternal complications, and specialist referrals all require this tier. People reasonably expect to travel further for specialist care than for emergency care, so the time bands are wider.

Time bands:

| Band | Score |
|------|-------|
| Within 60 min | 100 |
| 60 to 120 min | 70 |
| 120 to 180 min | 40 |
| 180+ min | 0 |

**Any facility access — 15% weight**

Basic primary care access still matters — for vaccinations, antenatal visits, malaria treatment, and first-contact care. But since almost every district in Ghana has a CHPS compound reasonably close by, this component does not differentiate districts as sharply as emergency or specialist access. It carries the lowest weight.

Time bands: same as emergency (30-minute threshold).

Each component produces a score between 0 and 100, weighted and combined into the final Journey Access Score.

---

## Score 2 — Supply Adequacy Score (0–100)

**What it answers:** *When people arrive at a facility, is there enough capacity to serve them?*

This score is built entirely from the E2SFCA results computed in Phase 3. The raw E2SFCA scores — which measure the ratio of facility supply to competing population demand — were normalised to a 0–100 scale. The district with the highest E2SFCA score receives 100. The district with the lowest receives 0. All other districts are scaled proportionally between them.

No other components are mixed into this score. It purely measures whether supply meets demand, independent of how long it takes to get there.

---

## Final Composite Score (0–100)

```
Final Score = (Journey Access Score × 70%) + (Supply Adequacy Score × 30%)
```

Journey access carries more weight because physically getting to care is the primary barrier in Ghana. The supply adequacy score plays a supporting role — it matters, but a district cannot compensate for terrible road access with good supply.

Critically, both sub-scores are always displayed alongside the composite score in the visualization. A policymaker looking at a district with a composite score of 45 needs to know whether that reflects a mobility problem (low journey score, adequate supply) or a capacity problem (good road access, overwhelmed facilities). The intervention required is completely different in each case.

---

## Categories

| Score | Category |
|-------|----------|
| 80 to 100 | Thriving |
| 60 to 79 | Decent |
| 40 to 59 | Getting By |
| 20 to 39 | Struggling |
| 0 to 19 | Dire |

---

## District Score Distribution

| Category | Districts |
|----------|-----------|
| Thriving | 26 |
| Decent | 161 |
| Getting By | 62 |
| Struggling | 9 |
| Dire | 2 |

The distribution reflects a country where most districts are functional but fragile — 161 in the Decent band — with a long tail of districts that are genuinely failing their populations.

---

## Score Extremes

| | District | Region | Composite Score |
|-|----------|--------|-----------------|
| Best | Bolga East | Upper East | 100.00 |
| Worst | East Gonja | Savannah | 11.55 |

Bolga East scores 100 because it sits directly adjacent to Bolgatanga Regional Hospital, giving it exceptional travel times to emergency and specialist care, combined with strong supply adequacy for its relatively small population.

East Gonja scores 11.55 because zero percent of its population can reach emergency care within 30 minutes, 71.8% take over 2 hours, 79.5% of all journeys happen on dirt roads, and E2SFCA supply adequacy is near zero. It is failing on every dimension simultaneously.

---

## Regional Scores

Computed directly from population-level data — not as an average of district scores — so that every person counts equally regardless of which district they live in.

| Region | Journey Access | Supply Adequacy | Composite | Category |
|--------|---------------|-----------------|-----------|----------|
| Upper East | 86.10 | 91.04 | 87.58 | Thriving |
| Upper West | 66.86 | 100.00 | 76.80 | Decent |
| Greater Accra | 96.62 | 20.07 | 73.66 | Decent |
| Ahafo | 85.09 | 39.43 | 71.39 | Decent |
| Bono | 77.94 | 44.44 | 67.89 | Decent |
| Volta | 88.64 | 11.11 | 65.38 | Decent |
| Western North | 65.65 | 59.86 | 63.91 | Decent |
| Western | 73.28 | 37.99 | 62.69 | Decent |
| Central | 86.87 | 3.58 | 61.88 | Decent |
| North East | 71.29 | 20.07 | 55.92 | Getting By |
| Ashanti | 78.14 | 0.00 | 54.70 | Getting By |
| Northern | 73.06 | 6.45 | 53.08 | Getting By |
| Eastern | 70.18 | 6.09 | 50.95 | Getting By |
| Oti | 67.22 | 6.45 | 48.99 | Getting By |
| Bono East | 52.66 | 13.26 | 40.84 | Getting By |
| Savannah | 54.10 | 6.81 | 39.91 | Struggling |

Two patterns emerge from this table that have direct policy implications.

**Northern regions struggle with mobility.** Savannah, Bono East, and Oti score poorly on Journey Access — meaning road infrastructure and facility placement are the primary barrier. The intervention required is physical: more facilities in underserved locations, better roads, or mobile health units.

**Southern regions struggle with supply.** Greater Accra has an exceptional Journey Access score of 96.62 — people can reach facilities quickly. But its Supply Adequacy score is only 20.07, because those facilities are overwhelmed by the capital's massive population. Central region has a Journey Access score of 86.87 but a Supply Adequacy score of just 3.58. Ashanti's supply adequacy is literally zero. The intervention required here is different: more staff, more equipment, expanded capacity at existing facilities.

This north-south pattern — mobility crisis in the north, capacity crisis in the south — is one of the most actionable findings of the entire project.

---

## Methodological Notes

**Why road quality is excluded from the composite score:**
Road conditions are already embedded in travel times via OSRM's speed assignments. A journey on a dirt track at 11 km/h already takes longer than the same distance on a major road at 45 km/h. Including road quality as a separate scoring component would penalise these districts twice for the same barrier. This decision was validated through a second-opinion review with Gemini (Google).

**Why median travel time is excluded:**
The time band percentages already capture the distribution of travel times within a district more precisely than a single median value. Two districts with the same median emergency travel time of 45 minutes can have very different distributions — one might have 40% within 30 minutes and 10% over 120 minutes, while the other has 0% within 30 minutes and everyone clustered around 45 minutes. The bands capture this distinction. The median does not.

**Why E2SFCA is kept as a separate sub-score:**
Mixing E2SFCA with travel time metrics in a single weighted average would compare apples and oranges. Travel time measures the mobility barrier. E2SFCA measures the capacity barrier. They answer different questions and require different policy responses. Keeping them as separate sub-scores — always displayed alongside the composite — ensures policymakers can identify which barrier is driving a district's low score.

**On the geometric mean alternative:**
A geometric mean would prevent a strong score in one component from masking a total failure in another. However, since both sub-scores are always displayed alongside the composite, policymakers can see the underlying breakdown directly. The linear weighted average was retained for its interpretability and transparency.

---

## Files Produced — Phase 4

| File | Description |
|------|-------------|
| `district_accessibility_scores.csv` | 260 districts × 42 columns — all time bands, road breakdowns, sub-scores, composite scores, and categories |
| `region_summary.csv` | 16 regions × 57 columns — same metrics at regional level, plus best and worst district per region |
| `district_scores.json` | District data in JSON format, ready for the visualization |
| `region_scores.json` | Region data in JSON format, ready for the visualization |

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

🔜 **Phase 2 — Exploratory Analysis - COMPLETE**

- 67% of all healthcare facilities in Ghana are basic CHPS compounds
- Central region has the worst facility-to-population ratio at 2.08 per 10,000 people
- Savannah region has the worst average distance at 32km — 7x further than Greater Accra
- 44x gap between the best-served district (North East Gonja) and the worst-served (GA South)
- GA South has 350,000 people and no hospital
- 38 districts (15%) have zero hospitals of any kind
- Private healthcare is concentrated in Greater Accra (35.6%) and virtually absent in Upper West (2.7%), Savannah (3.3%), and Oti (4.0%)
- Rural areas have more facilities per person but only 4.8% are hospitals vs 12.7% in urban areas
- Rural people travel nearly twice as far to reach healthcare (14.4km vs 7.6km)

These findings established the foundation for the deeper spatial accessibility analysis using the Enhanced Two-Step Floating Catchment Area (E2SFCA) method, which would go beyond centroid-based distance estimates to model actual travel times across Ghana's road network.

🔜 **Phase 3 — Six Accessibility Analyses - COMPLETE**

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
