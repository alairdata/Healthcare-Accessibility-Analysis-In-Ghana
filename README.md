# 🏥 Ghana Healthcare Accessibility Analysis

---

# TL:DR

Ghana has nearly 10,000 healthcare facilities. Most of them can't save your life.
Walk into almost any of them and you'll find a single room, one community health worker, and a box of malaria medication. No doctor. No surgery. No ambulance.
Have a stroke? Difficult birth? Car accident?
You're on your own — and the nearest hospital is, on average, 41 minutes away. On a dirt road. That floods in the rainy season. That no ambulance will attempt at night.
For 200,000 Ghanaians, there is no hospital within two hours. By any road. At all.
Meanwhile, MPs keep announcing projects. Regulatory bodies keep publishing plans. And nobody has had the data to call them out.
Until now.

What it is: A comprehensive spatial analysis of healthcare access across all 260 districts and 16 regions of Ghana, built from scratch using 9,978 facilities scraped from the Ghana Health Service platform.
The core finding: Ghana looks well-served on paper — 90.7% of the population lives within 30 minutes of a facility. But that facility is almost always a basic CHPS compound staffed by one community health nurse with no surgery, no doctor, no emergency equipment. The moment you need real care, access collapses:

Only 46.7% live within 30 minutes of a hospital
Mean travel time to the nearest specialist is 117.7 minutes
38 districts have zero hospitals entirely
210,000+ people have no healthcare access within 2 hours by any road

The north-south divide: Northern regions (Savannah, Bono East, Oti) struggle to get to care — too few facilities, long distances, mostly dirt roads. Southern regions (Greater Accra, Central, Ashanti) can reach facilities quickly but those facilities are overwhelmed — supply can't meet demand.
The composite score: Each district gets three scores: how long the journey takes, what the journey is actually like (dirt roads vs tarmac), and whether there's enough capacity when you arrive. Best district: Bolga East (96.81/100). Worst: East Gonja (13.74/100) — failing on all three dimensions simultaneously.
Why it was built: To give policymakers data they can actually be held accountable to, instead of vague promises.

---

# DETAILED DOCUMENTATION

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

# Phase 3.1 — Travel Time Analysis & Journey Breakdown

With the E2SFCA complete, the next step was to answer a more direct question: how long does it actually take each person in Ghana to reach the nearest healthcare facility — and how does that change depending on what type of care they need?

But travel time alone does not tell the full story. A journey of 35 minutes on smooth tarmac in Accra is not the same as 35 minutes on a dirt track in Savannah that floods in the rainy season, that no ambulance will attempt at night, and that destroys vehicle suspensions. The number on paper is the same. The lived reality is completely different.

This phase therefore has two parts: computing the travel times themselves, and then breaking down what those journeys actually look like by road type.

---

## The Seven Facility Groupings

I defined seven facility groupings for this analysis, each representing a different level of care:

| Grouping | Facilities | What it includes |
|----------|-----------|-----------------|
| Any | 9,978 | All facilities |
| CHPS | 6,733 | CHPS compounds only |
| Maternity | 6,981 | CHPS + maternity homes |
| Outpatient | 2,991 | Clinics + health centres + all hospitals |
| Emergency | 847 | All hospitals |
| Specialist | 28 | Regional + teaching + university hospitals |
| Psychiatric | 5 | Psychiatric hospitals only |

The groupings are deliberately nested and overlapping, not mutually exclusive. A hospital appears in Any, Emergency, and Specialist. A CHPS appears in Any, CHPS, and Maternity. This is intentional — the question for each grouping is: how long does it take to reach the nearest facility of this specific type?

**A note on specialist facility classification:** The specialist grouping uses the Ghana Health Service's own classification of facilities as Regional Hospital, Teaching Hospital, or University Hospital/Clinic. Some facilities in this category may reflect GHS platform classification conventions rather than strict clinical specialist capacity. For example, NKWANTA-NANDO CHPS appears in the GHS data as a Teaching Hospital. These have been used as-is with this caveat noted. A manual audit and reclassification of the specialist tier is identified as future work.

---

## The Routing Methodology — KD-Tree + OSRM Table API

For each of the 278,001 population grid cells, I needed to find the travel time to the nearest facility in each grouping. Routing from every population point to every facility would require billions of individual computations — computationally impossible. I used a two-stage approach to make this tractable while preserving accuracy.

### Stage 1 — KD-Tree Candidate Selection

For each population point, I used a KD-Tree spatial index to find the K nearest facilities by straight-line distance. This gives a candidate shortlist to pass to the router.

**Why K=20 and not K=10:**

The original computation used K=10. A sensitivity test on 200 sample points revealed that K=10 was finding the wrong nearest facility for 3.5% of points, with a maximum error of 74.88 minutes. This happens because Ghana has lungulungu roads — winding routes that go around rivers, hills, and forests. The nearest facility by straight line is not always the nearest by road. A facility 2km away by straight line might be 90 minutes away by road if there is a river between them, while a facility 8km away by straight line might be reachable in 12 minutes on a direct tarmac road.

I tested K=10, K=20, K=50, and K=100:
- K=20 fixed all serious errors that K=10 missed (3.5% of points, up to 74.88 minutes)
- K=50 and K=100 found no additional improvements over K=20
- K=20 is the sweet spot — wide enough to catch all meaningful cases, no wasted computation beyond that

The practical implication: a 74-minute error in emergency access is the difference between life and death. K=20 was worth the additional computation.

### Stage 2 — OSRM Table API Routing

Rather than making 20 separate OSRM route calls per population point (which would have taken approximately 174 hours total), I used OSRM's Table API. This sends one request per population point containing:
- 1 origin (the population point)
- 20 destinations (the KD-Tree candidates)

OSRM returns all 20 travel times at once, routing along the actual Ghana road network — accounting for road types, speeds, and connectivity. It cannot cross a river without a bridge. It cannot take a shortcut through a forest. It uses the same Ghana-corrected road speeds described in Phase 3's E2SFCA section.

This approach is 15x faster than separate route calls while maintaining full routing accuracy.

**For each population point and each grouping, I recorded:**
- `nearest_min` — the travel time to the true nearest facility by road
- `nearest_mean` — the average travel time across the 20 candidates
- `nearest_max` — the travel time to the furthest of the 20 candidates
- `nearest_fac_name`, `nearest_fac_type`, `nearest_fac_lon`, `nearest_fac_lat` — the identity and location of the nearest facility

Total computation time: approximately 11 hours for all 7 groupings. Zero failures.

---

## Key Findings — Travel Times

The results reveal a dramatic and sobering collapse in accessibility as the required level of care increases:

| Facility Type | Within 30 min | Mean travel time |
|---------------|---------------|-----------------|
| Any facility | 90.7% | 12.2 min |
| CHPS | 89.8% | 13.1 min |
| Maternity | 89.9% | 13.1 min |
| Outpatient | 73.5% | 24.2 min |
| Emergency | 46.7% | 41.2 min |
| Specialist | 6.6% | 117.7 min |

**A critical note on wording:** These are cell-weighted percentages. Each of the 278,001 population grid cells is treated as one unit. The correct phrasing is: *"X% of Ghanaians live in areas within 30 minutes of..."* — not *"X% of Ghanaians can reach..."*. This is standard spatial analysis methodology: the cell represents a populated area, and residents of that area inherit its accessibility characteristics. This is how the WHO, the Lancet Commission on Global Surgery, and the majority of spatial health accessibility literature frame these statistics.

The headline statistic — that 90.7% of Ghanaians live in areas within 30 minutes of a healthcare facility — is technically accurate. But it is deeply misleading. That facility is almost always a CHPS compound: a single room, one community health worker, limited supplies, no doctor, no surgery, no emergency equipment.

The moment you need real care, the numbers collapse. Only 46.7% of Ghanaians live in areas within 30 minutes of a hospital. The mean travel time to the nearest emergency facility is 41.2 minutes — already past the internationally recognised golden hour for emergency intervention. For specialist care, the mean travel time is 117.7 minutes. The nearest specialist hospital is nearly two hours away for the average Ghanaian.

---

## Worst Districts for Emergency Access

Some districts are not just underserved — they are in crisis:

| District | Region | Mean emergency time | Min time | Max time | Within 30 min |
|----------|--------|---------------------|----------|----------|---------------|
| East Gonja | Savannah | 139 min | 46.8 min | 198 min | 0.0% |
| Kwahu Afram Plains South | Eastern | 129 min | 18.0 min | 252 min | 1.1% |
| North Gonja | Savannah | 108 min | — | — | 0.0% |

East Gonja is the starkest case in the entire dataset. Not a single person in the entire district can reach emergency care within 30 minutes. Even the most favourably located person in the district — the one with the shortest travel time — takes 46.8 minutes. The worst off takes 198 minutes — over three hours. The mean is 139 minutes.

Kwahu Afram Plains South tells a different but equally alarming story. The minimum travel time is 18 minutes — so some people are relatively well served. But the maximum is 252 minutes — over four hours. Within one district, the gap between the best and worst served population is 234 minutes. That level of internal inequality within a single administrative unit is extreme.

---

## Journey Breakdown — What the Travel Time Actually Feels Like

### Why journey breakdown matters

OSRM gives us travel times calculated using Ghana-corrected road speeds. A 35-minute emergency journey has already been computed accounting for the fact that some of it is on dirt tracks at 11 km/h and some of it is on a highway at 45 km/h.

But the number alone does not communicate what that 35 minutes is like. A 35-minute journey that is 90% major roads is predictable, reliable, and navigable by an ambulance at night. A 35-minute journey that is 80% rural unpaved roads becomes much longer when it rains. Ambulances may refuse to attempt it. The vehicle may break down. The road may be washed out.

To expose this gap between the number on paper and the lived reality, I traced the actual route for each population point to its nearest facility and broke it down by road type.

### Computation method

Using the OSRM route API with full geometry and duration annotations, I retrieved the complete route path for each population point. For each route segment, I matched the segment midpoint to the nearest road in the OSM shapefile using a pre-built KD-Tree. This allowed me to assign each segment to one of five road type categories and attribute the corresponding travel time to that category.

I computed journey breakdowns for three facility types:
- **Any facility** — to understand the baseline daily access journey
- **Emergency facility** — to understand what rushing to hospital actually looks like
- **Specialist facility** — to understand the longest, hardest journeys

Total computation time: approximately 15 hours for all three breakdowns across 278,001 population points. One failure across all points.

### National findings — journey to nearest ANY facility

| Road Type | Average time | % of journey |
|-----------|-------------|--------------|
| Rural unpaved | 14.3 min | 64.8% |
| Connecting road | 5.0 min | 22.8% |
| Major road | 2.2 min | 10.0% |
| Urban road | 0.5 min | 2.3% |
| Walking | ~0 min | ~0% |

Nearly two thirds of the average Ghanaian's journey to the nearest healthcare facility — 64.8% — happens on dirt tracks and unpaved rural roads. Roads that travel at 5 to 20 km/h. Roads that become impassable in the rainy season. Roads that no ambulance will attempt at night.

### National findings — journey to nearest EMERGENCY facility

| Road Type | Average time | % of journey |
|-----------|-------------|--------------|
| Rural unpaved | 17.8 min | 43.4% |
| Connecting road | 14.6 min | 35.7% |
| Major road | 7.7 min | 18.8% |
| Urban road | 0.9 min | 2.1% |
| Walking | 0.0 min | 0.0% |

The emergency journey breakdown reveals something important: when people need to reach a hospital urgently, 43.4% of that journey is still on dirt roads. The journey is not just long — it is physically arduous, navigating the same unpaved infrastructure that defines everyday life in much of Ghana.

### National findings — journey to nearest SPECIALIST facility

| Road Type | Average time | % of journey |
|-----------|-------------|--------------|
| Major road | 63.6 min | 54.2% |
| Connecting road | 33.0 min | 28.2% |
| Rural unpaved | 19.1 min | 16.3% |
| Urban road | 1.3 min | 1.1% |
| Walking | 0.3 min | 0.2% |

The specialist journey tells a completely different story. 54.2% of the average specialist journey happens on major roads — because regional hospitals and teaching hospitals are located in major cities that are anchored to the national highway network. The journey to a specialist is long (117.7 minutes mean) but it is mostly on better roads.

### The insight these three breakdowns reveal together

Emergency journeys are chaotic — 43% dirt roads because you take whatever route gets you there fastest, regardless of surface. Specialist journeys are long but more structured — 54% major roads because those hospitals are far away but connected to the highway network. The everyday journey to the nearest CHPS is the worst of all — 64.8% dirt roads, because those facilities are scattered throughout rural communities and reached almost entirely by unpaved tracks.

Three completely different access realities. The travel time alone would never reveal this.

---

## District-Level Road Quality Variation

The national averages mask extreme variation at district level. Some districts have almost no dirt roads in their healthcare journeys. Others have almost nothing else.

**Most dirt-road dependent districts (journey to any facility):**
- Akatsi North (Volta): 88.1% rural unpaved
- Tatale Sanguli (Northern): 87.3% rural unpaved
- Nanumba South (Northern): 83.4% rural unpaved
- Sawla-Tuna-Kalba (Savannah): 82.0% rural unpaved
- East Gonja (Savannah): 79.5% rural unpaved

**Least dirt-road dependent districts:**
- Ayawaso Central (Greater Accra): 100% urban roads
- Old Tafo (Ashanti): 86.5% urban roads
- Ayawaso East (Greater Accra): 73.7% urban roads

A person in Ayawaso Central travels their entire healthcare journey on urban roads. A person in Akatsi North travels 88% of their journey on dirt. Same country. Same national health system. Completely different physical reality.

---

## Files Produced — Phase 3

| File | Description |
|------|-------------|
| `master_population_data_v2.csv` | 278,001 rows × 49 columns — the complete master file |
| `nearest_any_times_v2.csv` | K=20 travel times for any facility grouping |
| `nearest_chps_times_v2.csv` | K=20 travel times for CHPS grouping |
| `nearest_maternity_times_v2.csv` | K=20 travel times for maternity grouping |
| `nearest_outpatient_times_v2.csv` | K=20 travel times for outpatient grouping |
| `nearest_emergency_times_v2.csv` | K=20 travel times for emergency grouping |
| `nearest_specialist_times_v2.csv` | K=20 travel times for specialist grouping |
| `district_travel_times.csv` | Mean travel times per district for all four key facility types |


---
# Phase 4 — Composite Accessibility Index

With all computational phases complete, the final analytical step was to synthesise everything into a single, transparent, and defensible accessibility score for each of Ghana's 260 districts and 16 regions.

The challenge with composite scoring is that it is easy to build an index that looks rigorous but masks its assumptions. Every weight, every threshold, and every methodological decision in this framework is openly documented — so that a policymaker who disagrees with a weighting can understand exactly what they are disagreeing with and why.

---

## Why Three Scores

Healthcare accessibility is not one thing. It has three distinct dimensions that require separate measurement:

**The mobility barrier** — Can people physically get to a facility? How long does it take?

**The journey quality barrier** — What is the journey actually like? A 35-minute journey on tarmac is not the same as a 35-minute journey on a dirt track that floods in the rainy season, that ambulances will not attempt at night, and that becomes impassable for weeks at a time.

**The capacity barrier** — When people arrive, is there enough to serve them? A facility might be nearby and on a good road, but completely overwhelmed by the population it serves.

Each of these barriers requires a completely different policy intervention:
- Mobility problem → more facilities, better road placement, mobile health units
- Journey quality problem → road infrastructure investment, all-season road construction
- Capacity problem → more staff, more equipment, expanded facility funding

Collapsing all three into a single number without exposing the sub-scores would hide which problem a district is actually facing. All three sub-scores are always displayed alongside the composite.

---

## Score 1 — Journey Access Score (0-100)

**What it answers:** How long does it take to get to care?

The Journey Access Score is built from travel time band scores for three facility types, weighted by clinical urgency.

**Emergency access — 50% weight**

Emergency care is life or death. Missing the 30-minute golden hour for trauma, stroke, or obstetric emergency has direct mortality consequences. Emergency access carries the highest weight.

Time bands:

| Travel time | Band score |
|-------------|-----------|
| Within 30 min | 100 |
| 30 to 60 min | 70 |
| 60 to 90 min | 40 |
| 90 to 120 min | 15 |
| 120+ min | 0 |

**Specialist access — 35% weight**

Specialist care — regional hospitals, teaching hospitals, university hospitals — captures the quality gap. Chronic disease management, surgery, maternal complications, and specialist referrals all require this tier. People reasonably expect to travel further for specialist care than for emergency care, so the time bands are wider.

Time bands:

| Travel time | Band score |
|-------------|-----------|
| Within 60 min | 100 |
| 60 to 120 min | 70 |
| 120 to 180 min | 40 |
| 180+ min | 0 |

**Any facility access — 15% weight**

Basic primary care access still matters — for vaccinations, antenatal visits, malaria treatment, and first-contact care. But since almost every district in Ghana has a CHPS compound nearby, this component does not differentiate districts as sharply as emergency or specialist access. It carries the lowest weight.

Time bands: same as emergency (30-minute threshold).

**Formula:**
```
Journey Access Score = (Emergency band score × 50%) +
                       (Specialist band score × 35%) +
                       (Any facility band score × 15%)
```

---

## Score 2 — Journey Quality Score (0-100)

**What it answers:** What does the journey actually feel like?

This score uses the road type breakdowns computed in Phase 3 to measure the physical quality of the journey to healthcare. It is not about how long the journey takes — it is about what kind of infrastructure that time is spent on and how reliable, safe, and passable that infrastructure actually is.

**The scoring grid:**

For each journey type, I calculate the percentage of the journey spent on good roads (major road + connecting road + urban road) and bad roads (rural unpaved + walking). These two percentages are then scored using a lookup grid.

| % of Journey | Good Roads Score | Bad Roads Score |
|-------------|-----------------|-----------------|
| 0 to 10% | 10 | 100 |
| 11 to 30% | 40 | 70 |
| 31 to 50% | 70 | 30 |
| 50%+ | 100 | 0 |

```
Road Quality Score = (Good Roads Score + Bad Roads Score) / 2
```

The grid is designed so that scores never reach zero — a journey that is 100% dirt roads still scores 5/100, acknowledging that even an unpaved track represents some form of access. And scores scale smoothly enough that a 1-2% change in road mix does not cause a dramatic score cliff.

**Pressure testing:**

*65% good roads, 35% bad roads:*
Good score 100 + Bad score 30 = 65/100. A mostly good journey with a rough final stretch. Fair.

*85% good roads, 15% bad roads:*
Good score 100 + Bad score 70 = 85/100. Fast highway trip with a brief unpaved connector. Near excellent.

*25% good roads, 75% bad roads:*
Good score 40 + Bad score 0 = 20/100. Mostly dirt tracks. Correctly flagged as a challenging route.

**Journey Quality Score combines three journey types with the same weights as Journey Access:**

```
Journey Quality Score = (Emergency road quality × 50%) +
                        (Specialist road quality × 35%) +
                        (Any facility road quality × 15%)
```

Emergency journeys matter most — the road quality of an emergency journey has the most direct life-or-death implications. Specialist journeys matter significantly. Any-facility journeys provide baseline context.

---

## Score 3 — Supply Adequacy Score (0-100)

**What it answers:** When people arrive, is there enough capacity to serve them?

This score is built entirely from the E2SFCA scores computed in Phase 3. The raw E2SFCA score for each population point measures the ratio of facility supply to competing population demand within a 120-minute catchment, weighted by distance decay.

At the district level, I take the average E2SFCA score across all population points in the district. The district with the highest average E2SFCA score receives 100. The district with the lowest receives 0. All others are scaled proportionally between them.

```
Supply Adequacy Score = (district avg E2SFCA - national min) /
                        (national max - national min) × 100
```

Supply adequacy is kept as its own independent score because it measures something fundamentally different from the journey scores. Travel time and road quality describe what happens on the way to care. Supply adequacy describes what happens when you arrive. A policymaker looking at a low composite score needs to know immediately whether the problem is getting there or being served — the intervention required is completely different in each case.

**Note on E2SFCA scope:**
The E2SFCA was computed across all 9,978 facilities combined. A single supply adequacy score is used — not separate scores by facility type. Facility-type-specific E2SFCA scores (emergency-only, specialist-only) would require additional computation and are identified as future work.

---

## The Final Composite Score

```
Final Composite = (Journey Access Score  × 25%) +
                  (Journey Quality Score × 25%) +
                  (Supply Adequacy Score × 50%)
```

**On the weights:**

Journey access and journey quality together describe the journey to care — they each carry 25%, giving the journey a combined 50% weight. Supply adequacy describes what happens at the destination — it carries the remaining 50%.

This equal split between journey and destination reflects a core principle: healthcare accessibility is not just about getting there. It is about being served when you arrive. A district where residents can reach a hospital in 15 minutes on smooth tarmac but where the hospital is so overwhelmed it cannot see patients is not truly accessible. The weights ensure that capacity failures are not masked by excellent road infrastructure.

---

## Category Names and Thresholds

| Score | Category |
|-------|----------|
| 80 to 100 | Top Tier |
| 60 to 79 | Almost There |
| 40 to 59 | Managing |
| 20 to 39 | Struggling |
| 0 to 19 | Crisis |

The category names were designed to communicate a clear and unambiguous hierarchy to any reader — including non-technical policymakers. Top Tier, Almost There, Managing, Struggling, and Crisis communicate both rank and severity without requiring any background in data analysis to interpret.

---

## District Score Distribution

| Category | Districts |
|----------|-----------|
| Top Tier | 5 |
| Almost There | 57 |
| Managing | 163 |
| Struggling | 34 |
| Crisis | 1 |

Only 5 districts achieve Top Tier status — all in Upper East and Upper West. The vast majority of Ghana (163 districts, 63%) are Managing — functional but fragile. 34 districts are Struggling with meaningful failures across multiple dimensions. East Gonja stands alone in Crisis.

---

## Score Extremes

| | District | Region | Composite Score |
|-|----------|--------|-----------------|
| Best | Bolga East | Upper East | 96.81 |
| Worst | East Gonja | Savannah | 13.74 |

**Bolga East (96.81):**
Bolga East sits directly adjacent to Bolgatanga Regional Hospital — one of Ghana's well-resourced regional facilities. This gives it exceptional travel times to both emergency and specialist care on relatively good road infrastructure. Its small population means E2SFCA supply adequacy is also strong. It scores near-perfectly on all three dimensions simultaneously.

**East Gonja (13.74):**
East Gonja is failing on every single dimension:

- Journey Access (15.81): Zero percent of its population lives within 30 minutes of emergency care. Even the most favourably located person takes 46.8 minutes. The mean is 139 minutes. The worst is 198 minutes — over three hours.
- Journey Quality (35.75): 79.5% of all journeys are on dirt roads.
- Supply Adequacy (1.69): Almost no facility supply relative to population demand.

There is no single fix for East Gonja. It needs more facilities, better roads, more healthcare workers, and sustained investment — simultaneously.

---

## Regional Scores

Regional scores are computed directly from population-level data — not as an average of district scores — so that every person counts equally regardless of which district they live in.

| Region | Access | Quality | Supply | Composite | Category |
|--------|--------|---------|--------|-----------|----------|
| Upper East | 86.55 | 67.50 | 46.74 | 61.88 | Almost There |
| Upper West | 72.36 | 65.25 | 50.23 | 59.52 | Managing |
| Greater Accra | 97.15 | 87.25 | 18.84 | 55.52 | Managing |
| Western North | 65.91 | 72.75 | 34.48 | 51.90 | Managing |
| Ahafo | 85.33 | 67.50 | 26.39 | 51.40 | Managing |
| Bono | 78.46 | 67.50 | 28.40 | 50.69 | Managing |
| Western | 74.67 | 67.50 | 25.90 | 48.49 | Managing |
| Volta | 89.11 | 67.50 | 15.27 | 46.79 | Managing |
| North East | 75.72 | 67.50 | 18.81 | 45.21 | Managing |
| Central | 88.39 | 67.50 | 12.22 | 45.08 | Managing |
| Ashanti | 79.75 | 67.50 | 10.87 | 42.25 | Managing |
| Northern | 73.86 | 67.50 | 13.46 | 42.07 | Managing |
| Oti | 67.28 | 72.75 | 13.44 | 41.73 | Managing |
| Eastern | 72.33 | 67.50 | 13.32 | 41.62 | Managing |
| Savannah | 56.94 | 67.50 | 13.61 | 37.92 | Struggling |
| Bono East | 52.87 | 50.25 | 16.04 | 33.80 | Struggling |

**Two completely different policy problems emerge from this table.**

Northern regions — Savannah, Bono East, Northern, Oti — score poorly on Journey Access. Getting to care is the primary barrier. The required intervention is physical: more facilities in underserved locations, better road placement, mobile health units.

Southern regions — Greater Accra, Central, Volta, Ashanti — score well on Journey Access and Journey Quality but have critically low Supply Adequacy scores. Greater Accra has a Journey Access score of 97.15 — people can reach facilities quickly on excellent roads. But its Supply Adequacy is only 18.84, because those facilities are overwhelmed by the capital's massive population. Central scores 88.39 on access but only 12.22 on supply. Ashanti scores 10.87 on supply. The required intervention here is completely different: expanded facility capacity, more healthcare workers, more equipment.

This north-south pattern — a mobility and infrastructure crisis in the north, a capacity and overcrowding crisis in the south — is one of the most actionable findings of the entire project.

**Upper East — the surprising outlier:**

Upper East is the only region in Almost There (61.88) and the only Top Tier districts in the country are in Upper East and Upper West. This surprises people who associate these regions with poverty and underdevelopment. The explanation is the combination of a relatively small population, a reasonably dense health facility network, reasonable road connectivity for the region's size, and the presence of Bolgatanga Regional Hospital as an anchor facility. Small populations and proportionate facility coverage produce strong supply adequacy scores that larger, more densely populated regions cannot match.

---

## Methodological Notes

**On mean vs median:**
All travel time statistics use mean rather than median. The mean captures every person in the district — including those with extreme travel times at the margins. The median finds the middle person and ignores everyone worse off. For a project designed to hold policymakers accountable for the most underserved populations, mean is the more honest and powerful statistic.

**On E2SFCA normalization:**
The Supply Adequacy Score is normalized to 0-100 using the minimum and maximum E2SFCA scores across all 260 districts. Scores are relative, not absolute — a score of 50 means the district is halfway between the worst and best served districts in Ghana, not that it meets some international standard of adequate supply. This is appropriate for comparative within-country analysis.

**On the Penchansky and Thomas framework:**
This composite index measures three of the five dimensions in the Penchansky and Thomas healthcare accessibility framework: Accessibility (physical travel time), Availability (facility supply via E2SFCA), and a dimension of Accommodation (journey quality as a measure of infrastructure reliability). The remaining two dimensions — Affordability (transport costs, income constraints) and Acceptability (cultural and social fit of services) — are outside the scope of this analysis. Integrating economic and social variables following the full Penchansky and Thomas framework is the primary direction for Phase 2 of this project.

---

## Files Produced — Phase 4

| File | Description |
|------|-------------|
| `district_accessibility_scores.csv` | 260 districts × 59 columns — all time bands, road breakdowns, three sub-scores, composite scores, and categories |
| `region_summary.csv` | 16 regions × 51 columns — same metrics at regional level plus best and worst district per region |
| `district_scores.json` | District data in JSON format, ready for the visualization |
| `region_scores.json` | Region data in JSON format, ready for the visualization |
| `district_travel_times.csv` | Mean travel times per district for any facility, outpatient, emergency and specialist |


| `region_scores.json` | Region data in JSON format, ready for the visualization |

---

# README Updates — All Remaining Sections

---

## UPDATED: Current Status

✅ **Phase 1 — Data Cleaning & Validation — COMPLETE**

9,978 facilities across 260 districts in 16 regions. All coordinates validated against official administrative boundary polygons. All district labels corrected to match 2019/2021 administrative boundaries. Zero facilities over 100km from centroid.

✅ **Phase 2 — Exploratory Analysis — COMPLETE**

Key findings: 67% of all facilities are basic CHPS compounds. 38 districts have zero hospitals. Private healthcare is concentrated in Greater Accra (35.6%) and virtually absent in Savannah (3.3%) and Upper West (2.7%). Rural areas have more facilities per person but only 4.8% are hospitals vs 12.7% in urban areas.

✅ **Phase 3 — Spatial Accessibility Analysis — COMPLETE**

E2SFCA computed across 278,001 population points and 9,978 facilities using a 120-minute catchment and distance decay weights. K=20 OSRM travel times computed for all 7 facility groupings. Journey breakdowns computed for any facility, emergency facility, and specialist facility routes.

✅ **Phase 4 — Composite Accessibility Index — COMPLETE**

Three-score composite framework: Journey Access (25%) + Journey Quality (25%) + Supply Adequacy (50%). 260 districts and 16 regions scored and categorised. Best district: Bolga East (96.81). Worst district: East Gonja (13.74).

✅ **Phase 5 — Scrollytelling Visualization — COMPLETE**

Interactive scrollytelling visualization built in pure HTML/CSS/JavaScript using D3.js v7. Targeting policymakers and the public.

---

## Key Decisions Made

### Phase 1 decisions:
1. Zero duplicates — analysis confirmed no true duplicates after defining duplication logic as same name + location AND less than 1km apart
2. Trust polygon over hospital labels — for both region and district assignments
3. Create GUAN polygon manually — rather than skipping the 17 facilities in the newly created district
4. Update 1,030 district labels — corrected to match current administrative boundaries
5. Keep all facilities — nothing excluded, all coordinate issues resolved
6. Scrollytelling — chosen as final visualization format for policymaker impact

### Phase 3 decisions:
7. K=20 KD-Tree + OSRM Table API — sensitivity testing on 200 sample points showed K=10 missed the true nearest facility for 3.5% of points with maximum errors of 74.88 minutes. K=20 fixed all serious errors. K=50 and K=100 added nothing over K=20.
8. OSRM Table API over separate route calls — one API call per population point returning all 20 candidate travel times simultaneously. 15x faster than 20 separate calls with identical accuracy.
9. Journey breakdown computed for three route types — any facility, emergency facility, and specialist facility. Each tells a different story about the physical reality of healthcare access in Ghana.
10. Cell weighted percentages with correct wording — each 1km population grid cell represents a populated area. Residents inherit that area's accessibility. Correct wording: "X% of Ghanaians live in areas within 30 minutes of..." not "X% of Ghanaians can reach...". This is standard spatial analysis methodology.
11. Mean travel time over median — mean captures every person including those at the margins. Median finds the middle person and ignores everyone worse off. For a project holding policymakers accountable for the most underserved populations, mean is the more honest statistic.
12. Include all road types including walking paths — districts like Tempane (82.9% walking paths) and Krachi Nchumuru (70.3%) depend heavily on non-vehicle paths. Excluding them would disproportionately hurt already underserved regions.
13. Ghana-corrected road speeds — standard international speed assumptions are too generous for Ghana's actual road conditions. Speeds were reduced across all road types to reflect real travel conditions.

### Phase 4 decisions:
14. Three-score composite framework — Journey Access (25%) + Journey Quality (25%) + Supply Adequacy (50%). Three dimensions because mobility, journey quality, and capacity are fundamentally different barriers requiring different policy interventions.
15. Supply adequacy weighted at 50% — journey access and journey quality together describe the journey to care (combined 50%). Supply adequacy describes what happens at the destination (50%). Healthcare accessibility is not just about getting there — it is about being served when you arrive.
16. Option A scoring grid for journey quality — lookup grid based on percentage of journey on good roads vs bad roads. Scores never reach zero. Smooth enough that small changes in road mix do not cause dramatic score cliffs.
17. Category names: Top Tier, Almost There, Managing, Struggling, Crisis — communicates clear hierarchy to non-technical policymakers without ambiguity.
18. E2SFCA single score — not broken down by facility type. Single supply adequacy measure covering all facilities. Facility-type-specific E2SFCA is future work.
19. Specialist facility classification used as-is — GHS platform classification of Regional Hospital, Teaching Hospital, and University Hospital/Clinic used without modification. Some mislabeled facilities noted with methodology caveat. Manual audit identified as future work.

---

## UPDATED: Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core analysis language |
| Pandas | Data manipulation and cleaning |
| GeoPandas | Geospatial operations and polygon validation |
| Shapely | Geometry operations |
| Selenium | Web scraping from Ghana Health Service platform |
| FuzzyWuzzy | District name matching |
| igraph | Road network graph construction and E2SFCA computation |
| scipy (KD-Tree) | Spatial candidate selection for K=20 routing |
| numpy | Numerical computation |
| OSRM | Open Source Routing Machine — road network travel time computation |
| Docker | Running OSRM locally via container |
| WorldPop | Population density raster (1km resolution, 2020, normalized to 2021 census) |
| GADM | Administrative boundary shapefiles (Level 1 regions, Level 2 districts) |
| OpenStreetMap | Road network data (via Geofabrik Ghana extract) |
| D3.js v7 | Interactive map and data visualization |
| TopoJSON | Geographic boundary rendering in the browser |
| Scrollama | Scrollytelling framework |
| Jupyter Notebook | Development environment |
| python-Levenshtein | Speed optimization for fuzzy matching |

---

### Technical Stack

- Pure HTML, CSS, JavaScript — no React, no build tools, no server required
- D3.js v7 — map rendering, data binding, transitions
- TopoJSON v3 — geographic boundary rendering
- Opens directly in browser as a single HTML file
- Published via GitHub Pages

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

---

## NEW: Future Work — Phase 2

This project measures three of the five dimensions in the Penchansky and Thomas healthcare accessibility framework:

| Dimension | Status |
|-----------|--------|
| Accessibility — physical travel time | ✅ Measured |
| Availability — facility supply (E2SFCA) | ✅ Measured |
| Accommodation — journey quality | ✅ Measured |
| Affordability — economic barriers | ❌ Phase 2 |
| Acceptability — cultural and social barriers | ❌ Phase 2 |

**Phase 2 will extend the framework to include:**

**Affordability:**
Can people actually pay to access care? Transport fares, consultation fees, and drug costs are significant barriers for many Ghanaians — particularly in low-income districts. Phase 2 will integrate district-level economic variables (poverty rates, household income from Ghana Statistical Service) to model the economic dimension of accessibility alongside the physical dimension.

**Acceptability:**
Do the available facilities meet the cultural and social needs of the population they serve? Language barriers, gender of healthcare providers, traditional medicine preferences, and community trust in the formal health system all influence whether people seek care even when it is physically and economically accessible. Phase 2 will review the literature to identify the most appropriate and data-available social variables for the Ghanaian context.

**Facility-type specific E2SFCA:**
The current E2SFCA score covers all 9,978 facilities combined. Phase 2 will compute separate E2SFCA scores for emergency facilities and specialist facilities, allowing a more granular supply adequacy measure that distinguishes between the availability of basic care, hospital care, and specialist care.

Going full circle on the Penchansky and Thomas framework would make this one of the most comprehensive district-level healthcare accessibility analyses ever conducted for Ghana.

## Transparency Note

> The project log in this README was originally narrated verbally and transcribed with the help of AI (ChatGPT for transcription, Claude for writing assistance). The analysis, data collection, validation decisions, and all technical work are entirely my own.

## Author

**Princilla Abena Koranteng**
Data Scientist | Content Creator | [That Tech Girlie](https://www.linkedin.com/in/princilla-koranteng/)

*Last updated: June 11, 2026*

`#DataScience` `#HealthcareAnalysis` `#Ghana` `#GIS` `#GeoPandas` `#Python` `#ScrollyTelling` `#DataCleaning` `#SpatialAnalysis` `#PublicHealth` `#OpenData` `#2SFCA` `#PortfolioProject`

---

*If you're a policymaker, researcher, journalist, or just someone who cares about healthcare in Ghana — this project is for you.*
