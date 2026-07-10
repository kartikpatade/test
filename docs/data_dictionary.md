# Data Dictionary

Column reference for all 6 tables in `Custom_PowerBI_Agriculture_Model.xlsx`.

---

## dim_State

Central dimension table. One row per Indian state.

| Column | Type | Description | Example |
|---|---|---|---|
| State | Text | Full state name (Primary Key) | "Karnataka" |
| Region | Text | Geographic region | "South" |
| Contributor | Text | Researcher who authored this state's data | "Kartik Patade" |
| SourceSheet | Text | Original Excel sheet name | "Karnataka" |

---

## fact_Risks

Environmental and economic risk profiles. Exactly 2 rows per state (1 Environmental, 1 Economic).

| Column | Type | Description | Example |
|---|---|---|---|
| RiskID | Integer | Sequential primary key | 1 |
| State | Text | Foreign key → dim_State | "Assam" |
| RiskType | Text | "Environmental" or "Economic" | "Environmental" |
| Detail | Text | 3 bullet points, 14–21 words each, separated by newlines | "• Sand Casting & Soil Loss: The Brahmaputra basin..." |

**Notes:** 9 states were originally missing from this table. Their data was back-filled from fact_SWOT Threats (→ Environmental) and Weaknesses (→ Economic).

---

## fact_CropTimeline

Crop cultivation calendar by state. 511 rows total.

| Column | Type | Description | Example |
|---|---|---|---|
| TimelineID | Integer | Sequential primary key | 1 |
| State | Text | Foreign key → dim_State | "Punjab" |
| Crop | Text | Normalized crop name | "Wheat" |
| Season | Text | Kharif / Rabi / Zaid / Perennial / Multi-season | "Rabi" |
| SowingPeriod | Text | Sowing months as text | "October–November" |
| GrowthPeriod | Text | Growth duration | "4–5 months" |
| HarvestingPeriod | Text | Harvest months as text | "March–April" |
| PrimaryRegions | Text | Key growing districts/regions | "Ludhiana, Amritsar" |
| SowingMonthNum | Integer | Numeric sowing month (1–12) | 10 |
| HarvestMonthNum | Integer | Numeric harvest month (1–12) | 3 |

**Notes:** 332 of 511 Season values were blank (65%). All filled using `season_classifier.py` — an 80+ crop knowledge base with sowing-month fallback.

---

## fact_WaterEconomics

Crop water footprint and irrigation dependency data. 326 rows (2 garbage rows removed from Tripura).

| Column | Type | Description | Example |
|---|---|---|---|
| WaterID | Integer | Sequential primary key | 1 |
| State | Text | Foreign key → dim_State | "Karnataka" |
| Crop | Text | Normalized crop name | "Sugarcane" |
| WaterRequirementText | Text | High / Moderate / Low water requirement | "High water requirement" |
| IrrigationDependencyText | Text | Descriptive irrigation dependency label | "High" |
| WaterFootprint_Lkg_Avg | Float | Average water footprint in litres per kg | 200.0 |
| IrrigationDependencyRank | Integer | Rank on 1–8 scale (1=Low, 8=Extreme) | 6 |

**Notes:** 49 blank footprints and 70 blank ranks filled using `water_profiles.py` (ICAR + Mekonnen-Hoekstra published data).

---

## fact_SWOT

SWOT analysis entries per state. 269 rows.

| Column | Type | Description | Example |
|---|---|---|---|
| SWOTID | Integer | Sequential primary key | 1 |
| State | Text | Foreign key → dim_State | "Kerala" |
| Category | Text | Strengths / Weaknesses / Opportunities / Threats | "Strengths" |
| Detail | Text | 8–12 word executive headline | "Diverse spice production with strong export market demand." |

**Notes:** Original Detail values ranged from 13 to 654 words. All compressed to single-sentence headlines using `text_compress.py`.

---

## fact_AdvantagesConstraints

Structural advantages and constraints per state. 760 rows.

| Column | Type | Description | Example |
|---|---|---|---|
| RecordID | Integer | Sequential primary key | 1 |
| State | Text | Foreign key → dim_State | "Haryana" |
| Type | Text | "Advantage" or "Constraint" | "Advantage" |
| Detail | Text | ≤9 word sharp phrase | "Intensive irrigation infrastructure with ~81% coverage" |

**Notes:** 8 rows contained junk placeholders ("Details", "Analytical Details", "Factor"). Replaced with real content sourced from each state's fact_SWOT Strengths/Weaknesses entries.
