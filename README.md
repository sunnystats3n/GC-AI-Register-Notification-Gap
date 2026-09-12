# Personal Information and Notification Disclosure in the GC AI Register (MVP): A Data Governance Analysis

**Author:** Ogbonnaya Nzie Ezichi
**Context:** Self-directed data governance analysis, using the Government of Canada's own published open data. September 2026.
**Scope note:** All figures below come from one file — `gc-ai-register-mvp-registre-de-lia-du-gc-pmv-04-26.csv`, the April 28, 2026 edition of the Government of Canada AI Register (Minimum Viable Product), downloaded from the Open Government Portal. No other source was used for any number in this document. Where I could not verify something from this file, I say so rather than filling the gap.

---

## 1. Objective

The Government of Canada AI Register (MVP) is a self-reported inventory of AI systems in use or in development across federal departments, assembled by Treasury Board Secretariat from existing sources (Algorithmic Impact Assessments, Access to Information requests, Parliamentary Question responses, Personal Information Banks, and the GC Service Inventory). It is explicitly a minimum viable product — the dataset's own description states that "because the information included has been gathered through different instruments at different times, not all entries are complete, and formatting may vary."

I wanted to know whether that self-reported gap is measurable and specific, rather than a generic disclaimer: for the systems the register itself flags as touching personal information, how consistently is a corresponding notification also recorded? This is a narrower and more defensible question than asking whether any given system is "high-risk" or "compliant" — the register carries no risk-tier or impact-score field at all (see Section 5), so I did not attempt to answer that question here.

## 2. Data and Method

The source file has 412 system records across 43 federal organizations, with 24 columns delivered bilingually (English/French column pairs, plus one combined `government_organization` field of the form `"English name / French name"`). I split that combined field on `" / "` — verified safe across all 412 rows and all 43 distinct organization names — and dropped the French-language columns, producing a 15-column, English-only working file (`data/gc_ai_register_mvp_english.csv`). All other values were kept exactly as published, including inconsistent casing (`"In production"` vs. `"in production"`) and stray whitespace in categorical fields — see `data/data_notes.md` for the full list of data-quality observations from that step.

The analysis itself uses two columns as the register publishes them:

- `involves_personal_information` — Y / N / blank
- `notification_ai` — Y / N / blank

**Important caveat on field meaning:** the Open Government Portal publishes no data dictionary for this dataset (confirmed by checking both the dataset's metadata via the CKAN API and the accompanying change-log PDF — neither defines these columns beyond their names). I am reading `notification_ai` at face value, as "was notification of this AI system's use recorded." I cannot confirm from this file alone whether a blank means "not notified," "not yet assessed," or "not applicable," and I have treated blank and explicit "N" as two distinct categories throughout rather than collapsing them, precisely because I can't verify they mean the same thing.

A system was flagged as a **gap case** if `involves_personal_information = Y` and `notification_ai` was anything other than `Y` (i.e., explicitly `N` or blank).

## 3. Findings

Across all 412 systems: 88 (21.4%) are marked `involves_personal_information = Y`, 224 (54.4%) `N`, and 100 (24.3%) are blank. Separately, 196 systems (47.6%) have `notification_ai = Y`, 79 (19.2%) `N`, and 137 (33.3%) blank.

Of the 88 systems marked as involving personal information, **31 (35.2%) do not have a corresponding `Y` for notification** — 13 explicitly marked `N`, and 18 left blank. These 31 span **15 of the 43 departments** in the register. Thirteen of the 31 are already listed as **"In production"** — meaning, on the register's own status field, these are live systems, not proposals still in design:

| Register ID | Department | System | Status | Notification |
|---|---|---|---|---|
| 2526-CBSA-ASFC-002 | Canada Border Services Agency | Fuzzy Search (SSAName3) | In production | N |
| 2526-CBSA-ASFC-004 | Canada Border Services Agency | Primary Inspection Kiosk | In production | (blank) |
| 2526-CGC-CCG-001 | Canadian Grain Commission | Darktrace | In production | N |
| 2526-ESDC-EDSC-014 | Employment and Social Development Canada | Record of Employment Comments Assessment | In production | N |
| 2526-ESDC-EDSC-015 | Employment and Social Development Canada | Electronic SIN Automation | In production | (blank) |
| 2526-ESDC-EDSC-017 | Employment and Social Development Canada | Employment Insurance Machine Learning Workload | In production | (blank) |
| 2526-GAC-AMC-005 | Global Affairs Canada | Export Control On-Line System (EXCOL) | In production | N |
| 2526-GAC-AMC-022 | Global Affairs Canada | New Export Import Control System (New EICS) | In production | N |
| 2526-IRCC-013 | Immigration, Refugees and Citizenship Canada | Automation Tools to Help Process Privately Sponsored Refugee Applications | In production | (blank) |
| 2526-RCMP-GRC-002 | Royal Canadian Mounted Police | LASERi-X | In production | N |
| 2526-RCMP-GRC-004 | Royal Canadian Mounted Police | Text to speech | In production | N |
| 2526-TC-001 | Transport Canada | Pre-load Air Cargo Targeting (PACT) | In production | (blank) |
| 2526-VAC-ACC-001 | Veterans Affairs Canada | Automation Development to Support Disability Benefit Decision Making | In production | N |

The remaining 18 gap systems are split across "In development" (12), "Retired" (4), and blank status (2). The full list of all 31, with register IDs, is in `data/gap_systems.csv` so every row here can be checked directly against the source file.

For balance: the relationship is not simply "no personal information means no notification recorded" — 137 systems marked `involves_personal_information = N` still have `notification_ai = Y`, which suggests departments are recording notification for reasons beyond personal-information handling (or that the two fields are being filled in independently rather than as a linked pair). I'm reporting this because it argues against a stronger, more convenient claim I could have made — that notification is simply gated by personal-information status — and the data doesn't support that stronger claim.

## 4. What This Finding Does and Does Not Show

This is a **register-completeness finding, not a compliance finding.** It shows that in the Government of Canada's own published inventory, over a third of the systems that inventory itself flags as touching personal information do not have a matching "yes" recorded for notification. It does not show that any specific department failed to notify anyone, and it does not show that any of the 13 "In production" systems above are operating unlawfully — the register's blank cells could reflect incomplete self-reporting into this specific MVP tool rather than an actual absence of notification in practice (e.g., a department could have posted a public notice elsewhere that was never entered into this register). Establishing an actual notification failure for any one of these 31 systems would require checking that department's own disclosures, not just this spreadsheet cell.

What this finding does establish, and what I can defend line by line against the source file: the register itself, as published, does not currently let a reader confirm that notification was given for roughly a third of the systems it identifies as involving personal information — and for 13 of those, the system is already in production.

## 5. What I Deliberately Did Not Claim

An earlier version of this line of inquiry assumed the register (or a related source) would show an **Impact Level** and **Current Score** for a specific IRCC system, with named safeguards (Peer Review, GBA+, Notice, Training) becoming "Required" past a threshold. I checked: **this file has no such fields.** All 24 source columns are listed in `data/data_notes.md`; none of them carry an impact tier, a numeric score, or named mitigation-measure flags. That vocabulary belongs to Canada's Algorithmic Impact Assessment (AIA) tool, a separate output under the Directive on Automated Decision-Making — the register's own description confirms AIAs are one of several *inputs* used to compile this MVP, but the AIA results themselves were not carried into this CSV. If that specific score exists, it would need to be located in a published AIA for that system and cited on its own; I did not include it here because I have no way to verify it from this dataset.

## 6. Limitations

- **Self-reported, MVP-stage data.** The register's own documentation states entries were "gathered through different instruments at different times" and "not all entries are complete." A quarter of all rows are blank on `involves_personal_information` and a third are blank on `notification_ai` — this analysis only speaks to the 412 rows and two columns as published on 2026-04-28; it will be stale the moment the register is next updated.
- **No data dictionary.** Field meanings are inferred from column names only, not from an authoritative definition. If `notification_ai` tracks something narrower or broader than "notification of AI use to affected individuals," the interpretation in Section 3 would need to be revised.
- **Blank is not "No."** Everywhere in this document, blank and explicit "N" are reported as separate counts, never merged, because I cannot confirm from this file that they mean the same thing.
- **No department-size normalization.** Larger departments naturally have more AI systems and therefore more opportunities to appear in a raw count of gap cases; the department table above is a count, not a rate, and shouldn't be read as ranking departments by governance performance.

## 7. Skills This Demonstrates

- Deriving a checkable, narrow research question from a broad prompt, and abandoning a more dramatic but unverifiable claim (the AIA score) once the source data couldn't support it.
- Verifying a proposed governance claim against primary source data before publishing it, rather than accepting a plausible-sounding prior claim at face value.
- Distinguishing three practically different states in binary-looking fields (Y / N / blank) instead of collapsing them for a cleaner-looking result.
- Actively looking for and reporting a finding that cuts against the paper's own thesis (the 137 N/Y counter-examples in Section 3), rather than only reporting confirming evidence.
- Separating a data-completeness finding from a compliance finding, and stating plainly what evidence would be required to move from one to the other.

## 8. Reproducing This Analysis

```
data/gc_ai_register_mvp_english.csv   — English-only extract of the source CSV (412 rows, 15 columns)
data/data_notes.md                    — data-quality notes from the English-language extraction step
data/gap_systems.csv                  — the 31 gap-case rows referenced in Section 3, in full
```

Every number in Section 3 can be reproduced by cross-tabulating `involves_personal_information` against `notification_ai` in `data/gc_ai_register_mvp_english.csv`, treating blank as its own category.

## 9. Next Steps

1. Re-run this same cross-tab after the register's next scheduled update, to see whether the gap narrows, widens, or simply shifts to different departments — a single snapshot can't show a trend.
2. For a small sample of the 13 "In production" gap systems, check whether the responsible department has published notification through another channel (a public AIA summary, a Privacy Impact Assessment, a program webpage) that simply wasn't reflected back into this register — that would distinguish a reporting gap from a governance gap.
3. If a future edition of the register adds AIA impact-tier fields directly, repeat this analysis cut by tier instead of relying on personal-information status alone.
