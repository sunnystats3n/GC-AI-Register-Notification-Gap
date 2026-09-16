# Data Completeness & Integrity Audit: Analyzing Personal Information and Notification Disclosures in the GC AI Register

**Author:** Ogbonnaya Nzie Ezichi — [LinkedIn]() · [GitHub]() · [Google Scholar]()

---

### 📌 Project Executive Summary
This project delivers a rigorous, data-driven **Data Quality and Completeness Audit** of the Government of Canada (GC) AI Register (Minimum Viable Product). 

Using the Treasury Board Secretariat’s published dataset, this case study applies core **DAMA-DMBOK2 Data Quality dimensions**—specifically focusing on *completeness, validity, and cross-field consistency*—to evaluate systemic data-reporting gaps. Rather than auditing compliance abstractly, this repository executes a precise cross-tabulation of self-reported data fields to measure the visibility gap between system transparency claims and data reality in federal inventories.

### 🛡️ Core Audit Objective
The audit isolates a narrow, objective, and legally relevant question: **For AI systems that the register explicitly flags as interacting with personal information, how consistently is a corresponding notification disclosure recorded in the data profile?**

This methodology intentionally skips subjective compliance or risk-tiering definitions (which are absent from the schema metadata) and instead establishes a defensible, empirical critique of the dataset's internal structural integrity.

---

### 📊 Data Pipeline, Schema Engineering & Caveats
* **The Source Asset:** 412 system records across 43 federal organizations, tracked via a 24-column bilingual schema (`gc-ai-register-mvp-registre-de-lia-du-gc-pmv-04-26.csv`).
* **Data Cleansing & Normalization:** Built a reproducible ETL pipeline to isolate language variants. Handled complex whitespace anomalies and casing inconsistencies (`In production` vs. `in production`) without altering categorical metadata. The pipeline produces a normalized, 15-column English master file (`gc_ai_register_mvp_english.csv`).
* **Strict Semantic Controls:** In compliance with advanced data steward protocols, missing data dictionaries were handled cautiously. Blank cells and explicit `N` values were treated as distinct analytical categories to avoid over-indexing or mapping unverified assumptions onto structural gaps.

---

### 🔍 Key Audit Findings & Structural Discrepancies

#### 1. The Core Notification Gap
Out of 412 total institutional systems registered, **88 systems (21.4%)** are flagged as capturing personal data (`involves_personal_information = Y`).
* **The Discrepancy:** Of those 88 systems, **31 (35.2%) lack a corresponding confirmation** for recorded notification (`notification_ai != Y`). 
* **Triage Breakout:** 13 are explicitly marked `N` and 18 are completely blank, cutting across 15 separate government departments.

#### 2. Production Ingestion Exposures
Crucially, **13 of these 31 undocumented systems are already marked "In production"**, confirming they are live operational systems interacting with public workflows rather than safe-sandboxed proposals. These include high-impact systems within critical agencies:

| Register System ID | Responsible Agency / Department | Live AI System Asset Name | Operational Status |
| :--- | :--- | :--- | :--- |
| **2526-CBSA-ASFC-002** | Canada Border Services Agency | Fuzzy Search (SSAName3) | In production |
| **2526-CBSA-ASFC-004** | Canada Border Services Agency | Primary Inspection Kiosk | In production |
| **2526-ESDC-EDSC-014** | Employment & Social Development Canada | Record of Employment Comments Assessment | In production |
| **2526-IRCC-013** | Immigration, Refugees & Citizenship Canada | Private Sponsored Refugee Processing Automation | In production |
| **2526-RCMP-GRC-002** | Royal Canadian Mounted Police | LASERi-X Analytics Platform | In production |
| **2526-VAC-ACC-001** | Veterans Affairs Canada | Disability Benefit Decision Support Automation | In production |

*The full lineage of all 31 flagged assets is mapped line-by-line within [gap_systems.csv](gap_systems.csv) for direct source verification.*

#### 3. Balanced Data Observations
The relationship is not a simple linear dependency. The audit revealed **137 systems** where `involves_personal_information = N` but `notification_ai = Y`. This indicates that departments record notifications independently of personal-data flags, proving that fields are being filled out out-of-sync rather than through an automated, linked metadata validation sequence.

---

### ⚖️ Professional Interpretation (What This Proves)
* **What it IS:** A definitive finding on **Register Completeness and Data Catalog Integrity**. It proves that the government's centralized tracking portal cannot reliably verify table transparency for over a third of its active personal-data processing networks.
* **What it IS NOT:** A claim of legal non-compliance or unlawful behavior. Blank data cells can indicate an incomplete self-reporting pipeline into this specific software interface, rather than a failure to notify citizens in real life. This differentiation represents the exact analytical boundary and risk-mitigation framing expected of a Second-Line Data Governance function.

---

### 🗂️ Execution & Audit Reproducibility
The data parsing, profiling, and validation layers can be fully run using the provided repository modules:

```bash
# View custom data quality anomalies and text casing logs
cat docs/data_notes.md

# Review full gap matrix cross-check
cat outputs/gap_systems.md
```
