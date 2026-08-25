# Towards SDS — Terrestrial/Marine column harmonization

`Level1_SDS-aligned.xlsx` through `Level4_SDS-aligned.xlsx`, `Terrestrial_Master_SDS-aligned.xlsx`
and `CHANGES_SDS-aligned.xlsx` in this folder started life as staging copies of
the production tables with headers renamed toward SKOS/SDS terminology and
toward the marine domain's column naming, ahead of the terrestrial/marine
harmonization call (Serge Gofas & Paco Pando, 25 Aug 2026, 09:00 CEST).

**As of 25 Aug 2026, that staging distinction is gone.** `Terrestrial_Master_SDS-aligned.xlsx`
is now the master source of truth for terrestrial data content, and the
production tables one level up (`../Level1_21-Aug-26.xlsx` … `../Level4_20-Aug-26.xlsx`,
`../CHANGES_20-Aug-26.xlsx`) have been brought in line with it: same
SDS-aligned column names, same content, same valid-units-only filtering.
The two sets of files are now identical in content, differing only in
filename (production keeps its dated filenames) and location.

`Terrestrial_Master_SDS-aligned.xlsx` consolidates all four levels into a
single table (1,047 rows, valid current units only) — settling the "one
table per level vs. one table for everything" question *within* the
terrestrial domain (independently of whatever the marine side does, and
independently of whether terrestrial and marine end up as one SDS standard
or two — both still open).

`CHANGES_SDS-aligned.xlsx` carries the historical changelog (additions,
boundary changes, code changes, deletions, renames, transfers) — unlike the
master/level tables, it is **not** filtered to valid-only, since its purpose
is to be the complete historical record including deletions.

## Context

Prompted by Serge Gofas's email of 23 Aug 2026 ("Re: Getting closer to
finalising Geoschemes"), which maps the terrestrial columns onto the marine
domain's proposed SDS-oriented scheme, and onto the columns of Marine
Regions' own shapefile attribute table, and onto the marine proposal file
(`../../marine/Geoschemes_marine_proposal_12_2024.xls`).

## Rename mapping applied

| Terrestrial (old) | Terrestrial (new) | Marine equivalent | SDS / SKOS equivalent |
|---|---|---|---|
| L1/L2/L3/L4 code (the unit's own code) | `term_localName/`<br>`controlled_value_string` | `term_localName/controlled_value_string` | controlled value string (`rdf:value`) |
| L1 continent / L2 region / L3 area / L4 area (the unit's own name) | `label` | `label` | `rdfs:label` |
| L1 code *(as parent reference in Level2)* | `Level_1` | `Level_1` | `skos:broader` |
| L2 code *(as parent reference in Level3)* | `Level_2` | `Level_2` | `skos:broader` |
| L3 code *(as parent reference in Level4)* | `Level_3` | `Level_3` (= `skos_broader`) | `skos:broader` |
| L1/L2/L3/L4 ISOcode | `ISO_ter1` | `ISO_ter1` | territory ISO 3166-1 code |

Left unchanged (no marine/SDS equivalent identified in the email, or
terrestrial-specific): `Id`, `In Ed. 2`, `Change according to page 9`,
`Solved`, `Doubt`, `Revision notes`, `ISSUES`.

## Marine-aligned columns added (25 Aug 2026)

To match the marine proposal file's own column set, the master and all four
per-level tables (Towards_SDS and production alike) also gained:

- **`type`** — `"concept"` on every row.
- **`external url`** — new, currently blank.
- **`parent unit name`** — the immediate parent's `label`, resolved from
  `skos_broader` (or `Level_1`/`Level_2`/`Level_3` in the per-level files).
  Blank for Level 1 (continents have no parent).

## Consolidated master table

`Terrestrial_Master_SDS-aligned.xlsx` merges the four renamed tables above
into one sheet (`tblGeoschemesTerrestrial`), with two columns replacing the
per-level bookkeeping:

- **`Level`** (1–4) — which of the original tables the row came from.
- **`skos_broader`** — the immediate parent's local-name code, replacing
  the level-specific `Level_1` / `Level_2` / `Level_3` parent-reference
  columns. Blank for Level 1 (continents have no parent).

Every other column carries straight across, populated where the source
table had it and blank where it didn't. Level 1's old `Exchange notes`
column and Level 3/4's `Revision notes` column are folded into one shared
`Revision notes` column in the master (each per-level file keeps its own
original column name for that field).

## Valid-units filtering

The master table and all four per-level tables (both here and in
production) contain only currently valid units. A unit is excluded if its
`Change according to page 9` value contains the code `D` ("Deleted unit,"
per the Legend sheet in `CHANGES_SDS-aligned.xlsx`). Only two units are
recorded as deleted in the full CHANGES log: Sudan's old undivided `SUD-OO`
(superseded by `SUD-SD` + `SUD-SS`) and Réunion's old undivided `REU-OO`
(superseded by `REU-RE` + `REU-TR`). `REU-OO` was already absent from the
live tables; `SUD-OO` has been removed. Level4 also had 386 fully-blank
padding rows (Excel formatting artifacts predating this whole process) —
these are dropped everywhere too. Real Level4 content: 617 rows.

## Issue references normalized

`GSTerr9`-style internal references were replaced with `issue #10`
throughout (level tables, master, CHANGES `NOTES`), using the
GSTerr→GitHub-issue mapping established earlier in this project.

## Definitions populated (25 Aug 2026)

Every row (1,047) now has a `Definition`. Levels 1–3 are composed
mechanically from the hierarchy already in the table: a unit with more than
one child reads "The territory comprising A, B, and C." (e.g. `SPA` →
"The territory comprising SPA-AN, SPA-GI, and SPA-SP."); a unit with
exactly one child — the common case for Level 3, where ~80% of units are a
single undivided country — inherits that child's own definition instead
(e.g. `AFG` → "Afghanistan"), cascading up through multiple levels where
needed. Level 4 (617 leaf units) was researched individually, short
descriptive phrases in the style of "Continental Spain," web-verified where
the unit wasn't a well-known place.

A handful of disputed-sovereignty Level 4 definitions (Navassa Island, the
Paracel and Spratly Islands, Tromelin, Abkhazia, Nagorno-Karabakh, Western
Sahara) originally included a "disputed territory" qualifier in the
definition text itself; at Paco's direction these were trimmed to just the
place name/description, leaving the political framing out of the
definition (e.g. `TCS-AB` → "Abkhazia," not "Abkhazia, a breakaway region of
Georgia").

**Pre-existing data issue found and fixed in passing:** `ISO_ter1` for
`CZE-CZ` (Czechia) and `CZE-SK` (Slovakia) were swapped (Czechia had `SK`,
Slovakia had `CZ`) in every source file, predating this whole process.
Corrected to `CZ`/`SK` respectively.

## Deliberately NOT done here (open questions for the 25 Aug call)

- **ISO code format (alpha-2 vs. alpha-3).** Serge's older mapping equated
  terrestrial's ISOcode to marine's alpha-2 code; the newer shapefile-based
  scheme uses `ISO_ter1` = alpha-3. Terrestrial's current ISOcode values
  have not been re-verified against either format (aside from the CZE
  swap above).
- **Overlapping-claim ISO columns (`ISO_ter2`, `ISO_ter3`).** Not added here
  pending a decision on scope.
- **Marine-only column not yet mirrored in terrestrial**: `source`. Not
  added here.

## Files

Towards_SDS (this folder):
- `Level1_SDS-aligned.xlsx` (9 units)
- `Level2_SDS-aligned.xlsx` (52 units)
- `Level3_SDS-aligned.xlsx` (369 units)
- `Level4_SDS-aligned.xlsx` (617 valid units)
- `Terrestrial_Master_SDS-aligned.xlsx` (1,047 valid units in one table)
- `CHANGES_SDS-aligned.xlsx` (full changelog incl. deletions, 55 rows,
  `Legend` sheet intact)
- This `README.md`

Production (`../`, one level up) — now content-identical to the above,
under their existing dated filenames:
- `Level1_21-Aug-26.xlsx`, `Level2_21-Aug-26.xlsx`, `Level3_20-Aug-26.xlsx`,
  `Level4_20-Aug-26.xlsx`, `CHANGES_20-Aug-26.xlsx`
