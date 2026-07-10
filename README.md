# IRBAS — Agricultural Intelligence Dashboard

**28-state Indian agricultural resource and intelligence dashboard — crop water analytics, irrigation risk scoring, and state-level SWOT profiling.**

🌐 **Project Page:** https://soumodip18.github.io/IRBAS-Agricultural-Intelligence-Dashboard  
📊 **Live Dashboard:** https://app.powerbi.com/groups/me/reports/a0292903-1432-4493-9f5f-a8a94678a6e2  
🏫 **Institution:** M.Sc. Data Science · TU Dortmund University · 2026  
🏢 **Organisation:** IRBAS Commercial Operations

---

## What This Project Does

This pipeline collects, cleans, and visualises agricultural intelligence data across 28 Indian states. It answers two questions:

1. **Where are things happening?** — State-level risk alerts, SWOT profiles, and mandi price comparisons on an interactive map.
2. **What are the resource pressures?** — Crop water footprints, irrigation dependency scores, and seasonal cultivation calendars.

---

## Repository Structure

```
IRBAS-Agricultural-Intelligence-Dashboard/
│
├── index.html                          ← GitHub Pages project site
├── README.md
├── LICENSE
│
├── dashboard/
│   ├── india_crop_water_resource_dashboard-kartik.pbix
│   ├── executive_commodity_analysis-malvika.pbix
│   └── commodity_price_intelligence_dashboard-lavanya.pbix
│
├── data/
│   ├── Custom_PowerBI_Agriculture_Model.xlsx   ← Normalized star schema (6 tables)
│   ├── State Assessment Dashboard Agriculture.xlsx  ← Raw 29-sheet source
│   └── India_Master_Mandi_DB.xlsx              ← 91,000+ mandi price rows
│
├── scripts/
│   └── cloud_pipeline.py               ← Selenium scraper + Google Drive uploader
│
├── docs/
│   └── data_dictionary.md              ← Column reference for all 6 tables
│
└── .github/
    └── workflows/
        └── scrape.yml                  ← Scheduled GitHub Actions scraper
```

---

## Data Model

Star schema with 1 dimension table and 5 fact tables. All relationships run through `dim_State[State]`.

| Table | Rows | Description |
|---|---|---|
| `dim_State` | 28 | State, Region, Contributor, SourceSheet |
| `fact_Risks` | 56 | Environmental & Economic risks — 3 bullets per state per type |
| `fact_CropTimeline` | 511 | Crop, Season, Sowing/Harvest periods — 0 blanks |
| `fact_WaterEconomics` | 326 | Water footprint (L/kg), Irrigation Dependency Rank (1–8) |
| `fact_SWOT` | 269 | Strengths, Weaknesses, Opportunities, Threats per state |
| `fact_AdvantagesConstraints` | 760 | Structural advantages and constraints per state |

**Data quality work:** 87 issues fixed including 332 blank Season values (65%), 49 blank water footprints, 70 blank irrigation ranks, 8 junk placeholder rows, and 9 states missing from fact_Risks.

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/soumodip18/IRBAS-Agricultural-Intelligence-Dashboard.git
cd IRBAS-Agricultural-Intelligence-Dashboard
```

### 2. Install dependencies

```bash
pip install selenium webdriver-manager pandas openpyxl \
            google-auth google-api-python-client
```

### 3. Run the scraper locally

```bash
python scripts/cloud_pipeline.py
```

This scrapes all 30 state portals and saves `India_Master_Mandi_DB.csv`, then uploads to Google Drive. Requires `GCP_CREDENTIALS` environment variable set to your Service Account JSON.

### 4. Open the Power BI dashboard

- Download the `.pbix` from `dashboard/`
- Open in Power BI Desktop
- Update data source path: **Transform Data → Data Source Settings → Change Source** → point to `data/Custom_PowerBI_Agriculture_Model.xlsx`

### 5. GitHub Actions (automated scraping)

Add your Google Cloud Service Account JSON as a repository secret named `GCP_CREDENTIALS`. The workflow in `.github/workflows/scrape.yml` runs the scraper on a schedule.

---

## Embedding the Dashboard

Once the Power BI admin restriction is lifted (college IT or corporate account), generate the embed code via:

**Power BI → Share → Embed → Publish to Web**

Then replace the placeholder in `index.html`:

```html
<iframe
  src="YOUR_EMBED_URL_HERE"
  width="100%"
  height="800"
  allowFullScreen="true"
  style="border:none;">
</iframe>
```

---

## DAX Measures Reference

| Measure | Purpose |
|---|---|
| `Selected State Key Risk` | Displays Environmental + Economic risk bullets for selected state |
| `Gauge Irrigation Score` | Average IrrigationDependencyRank (1–8) for selected state |
| `% High Irrigation Dependency` | % of crops with rank ≥ 6 |
| `National Avg Irrigation Score` | Benchmark using REMOVEFILTERS |
| `Dynamic Profile Title` | Auto-updates page header on state selection |
| `Avg Water Footprint` | Average L/kg for selected state/crop |

---

## Contributors

| Name | Role | States Covered |
|---|---|---|
| Kartik Patade | Data Engineering, ETL, Normalization, Dashboard | Karnataka, Tamil Nadu, AP, Telangana, Odisha, Kerala, Goa, Arunachal Pradesh, Punjab, Haryana, MP, Assam, Chhattisgarh, Sikkim, Mizoram, Nagaland |
| Nada Khan | State Research | 7 states — North & West India |
| Lavanya Dive | State Research, Price Intelligence Dashboard | 7 states — Central India |
| Malavika Nair | State Research, Commodity Dashboard | 7 states — East & Northeast India |
| Soumodip Atanu Roy | Project Mentor | IRBAS Managing Director |

---

## License

MIT — see `LICENSE` for details.
