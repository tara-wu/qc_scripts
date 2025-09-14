# Trail Feature QC Scripts

## Overview
This repository contains Python scripts to perform quality assurance (QC) on trail feature data. The initial focus is on **Point QC**, with planned extensions for **Line QC** and **Polygon QC** workflows. These scripts help ensure data integrity before uploading to ArcGIS Online (AGOL) or using in GIS analysis.

---

## Contents

- **qc_points_agol.py** – Checks for nulls, invalid dates, orphaned/missing related records, domain violations, repetitive entries, proximity errors, and collector-region mismatches for point features.
- **qc_lines_agol.py** *(planned)* – QA for line features, including geometry checks and proximity buffers.
- **qc_polygons_agol.py** *(planned)* – QA for polygon features, including topology and attribute checks.
- **requirements.txt** – Python packages required for all scripts.
- **outputs/** – Folder where QC results (TXT and XLSX) are saved.

---

## Dependencies

Install packages from `requirements.txt`:

```bash
pip install -r requirements.txt
```

Required packages include:
- **pandas**
- **geopandas**
- **numpy**
- **arcgis**
- **shapely**
- **xlswriter**

## Logging & Output

- Logging is configured to write to both **console** and an optional **TXT log file**.
- Set `WRITE_LOG = True` to write messages to a log file inside the `outputs/` folder.
- The script produces a **master XLSX** of all errors with the current date in the filename.
- Example output files:
  - `outputs/YYYY-MM-DD_QC_summary.txt`
  - `outputs/YYYY-MM-DD_all_errors.xlsx`
- Console messages will still appear if `WRITE_LOG = False`, so users know the script ran successfully.

---

## CRS Assumptions

Different base layers may have different Coordinate Reference Systems (CRS). Current assumptions in the Point QC script:

| Layer Type               | CRS in Script | Notes |
|---------------------------|---------------|-------|
| Point Features (FC, RT)   | EPSG:3857     | Projected to match line features for proximity checks |
| Line Features (Tread, Side Trails) | EPSG:4269 | Reprojected to EPSG:3857 during analysis |
| Other Point Layers        | EPSG:4269     | Reprojected to EPSG:3857 for buffer calculations |

> Always verify the CRS of new datasets before running QA scripts. Reprojection is done internally via `to_crs(epsg=3857)`.

---

## Parameters

All configurable constants are grouped at the top of the script:

- `BUFFER_FT` – Buffer distance in feet for proximity checks.
- `THRESHOLD` – Maximum allowed repetition of a value per user.
- `PROJECT_START_YEAR/MONTH/DAY` – Start date for date validation.
- `FC_REQUIRED_FIELDS`, `RT_REQUIRED_FIELDS` – Fields that cannot be null.
- `FC_DOMAIN_DICTIONARY`, `RT_DOMAIN_DICTIONARY` – Acceptable domain values.
- `COLLECTOR_DICT` – Mapping of users to allowed trail regions.

---

## Running the Script

```bash
python qc_points_agol.py
```

## Outputs

All outputs will appear in the `outputs/` folder.
Optional log file messages appear if `WRITE_LOG = True`.

## Error Categories

The script checks for the following types of issues:

- **Null fields**
- **Date issues**
- **Missing/orphaned related records**
- **Domain violations**
- **Repetitive values**
- **Proximity issues**
- **Collector-region mismatches**

## Future Work

- Implement `line_qc.py` for line features.
- Implement `polygon_qc.py` for polygon features.
- Improve reproducibility for different CRS and local file sources (QGIS-ready versions).

## Notes

- Designed for ArcGIS Online Feature Layers, but can be adapted to local shapefiles using `geopandas.read_file()`.
- Always check the console/log outputs for warnings or errors.
- Maintain `requirements.txt` for reproducibility across team members.


