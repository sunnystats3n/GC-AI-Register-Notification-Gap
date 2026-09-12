# Data Notes — GC AI Register (MVP), English extract

**Source:** Government of Canada AI Register (Minimum Viable Product), Open Government Portal.
**Original file:** `gc-ai-register-mvp-registre-de-lia-du-gc-pmv-04-26.csv` (as supplied by the user, 412 data rows, 24 columns, UTF-8 with BOM — no encoding issues, unlike the CIHI file).

## What was found in the raw file

- **Bilingual structure:** most fields come as explicit `_en`/`_fr` column pairs (e.g. `name_ai_system_en` / `name_ai_system_fr`). One shared column, `government_organization`, instead packs both languages into a single cell as `"English name / French name"` (e.g. `"Health Canada / Santé Canada"`) — confirmed this split is safe: all 412 rows produced exactly two parts on `" / "`, and the 43 unique organizations all follow the pattern. `vendor_information` also contains a literal `" / "` in one row (`"D2L / Lumi"`), but that's two vendor names, not a language pair — it was left untouched.
- **`status_date` is a year, not a date:** despite the name, its 16 distinct non-blank values are all bare 4-digit years (1994–2026), with no month or day ever present.
- **Inconsistent casing/whitespace in categorical fields**, kept as-is in this extract per your preference not to silently clean source data: `ai_system_status_en` has `"In development"`, `"In development "` (trailing space), `"In production"`, `"In production "`, and one lowercase `"in production"`; `developed_by_en` has `"Other"` and `"Other "`. If you later aggregate or filter on these fields, they won't group correctly until normalized — flagging it rather than fixing it silently.
- **No French-language leakage** into the `_en` text fields — checked description/capabilities/results/etc. for common French stopwords and found none.
- **Sparse fields:** several columns are mostly or entirely blank for a given row (e.g. `developed_by_en`, `vendor_information`, `data_sources_en`, `personal_information_banks_en`, `ai_system_results_en` are empty for the IRCC entry checked below) — this looks like incomplete self-reporting by departments, not a parsing error.

## What was produced

- **`gc_ai_register_mvp_english.csv`** — same 412 rows, English-only: all `_fr` columns dropped, `government_organization` replaced by `government_organization_en` (the English half only). All other values kept exactly as sourced, including the casing/whitespace quirks noted above.

## Important finding re: the IRCC entry you referenced

I looked up **"Advanced Analytics Triage of Visitor Record Applications"** (`ai_register_id: 2526-IRCC-008`, Immigration, Refugees and Citizenship Canada) in this file — it's real and it is in the register. But this dataset's schema has **no Impact Level, Current Score, Peer Review, GBA+, Notice, or Training fields at all** — I listed all 24 source columns and none of them carry that information. The only related fields for this row are `involves_personal_information: Y` and `notification_ai: Y`; `ai_system_status_en` is `"In development"`.

The Impact Level / Current Score / mandatory-measures framework you described (Peer Review, GBA+, Notice, Training flipping to "Required" at a threshold) is the vocabulary of Canada's **Algorithmic Impact Assessment (AIA)** tool under the Directive on Automated Decision-Making — a separate assessment output, not a field in the AI Register. If that Impact Level 2 / Score 41 figure came from an actual published AIA for this system, it would need to be sourced separately (departments sometimes publish AIA summaries on canada.ca or via the `canada-ca/aia-eia-js` results) and cited independently — it isn't something this Register file can confirm or deny, and I have no record of having verified it myself. Treat it as unverified until traced to its own source.
