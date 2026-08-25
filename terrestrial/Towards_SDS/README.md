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
Regions' own shapefile attribute table.

## Rename mapping applied

| Terrestrial (old) | Terrestrial (new) | Marine equivalent (per Serge's email) | SDS / SKOS equivalent |
|---|---|---|---|
| L1/L2/L3/L4 code (the unit's own code) | `Local_name` | `Local_name` = `term_localName` | controlled value string (`rdf:value`) |
| L1 continent / L2 region / L3 area / L4 area (the unit's own name) | `Full_name` | `Full_name` = `label` | `rdfs:label` |
| L1 code *(as parent reference in Level2)* | `Level_1` | `Level_1` | `skos:broader` |
| L2 code *(as parent reference in Level3)* | `Level_2` | `Level_2` | `skos:broader` |
| L3 code *(as parent reference in Level4)* | `Level_3` | `Level_3` (= `skos_broader`) | `skos:broader` |
| L1/L2/L3/L4 ISOcode | `ISO_ter1` | `ISO_ter1` | territory ISO 3166-1 code |

Left unchanged (no marine/SDS equivalent identified in the email, or
terrestrial-specific): `Id`, `In Ed. 2`, `Change according to page 9`,
`Solved`, `Doubt`, `Revision notes`, `ISSUES`. `Definition` was already
present under that name and needed no change.

## Consolidated master table

`Terrestrial_Master_SDS-aligned.xlsx` merges the four renamed tables above
into one sheet (`tblGeoschemesTerrestrial`), with two columns replacing the
per-level bookkeeping:

- **`Level`** (1–4) — which of the original tables the row came from.
- **`skos_broader`** — the immediate parent's `Local_name` code, replacing
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

## 25 Aug 2026 update — production brought in line with master

Paco edited `Terrestrial_Master_SDS-aligned.xlsx` and `CHANGES_SDS-aligned.xlsx`
directly (as `...-25-08-26` dated working copies) to resolve four
long-standing review items and log one unit rename:

- **AGE-DF** (Argentina) renamed to **Ciudad Autónoma de Buenos Aires**,
  tagged `N` (Name change) in `Change according to page 9`, marked
  `Solved = Yes`. Logged as a new row in `CHANGES_SDS-aligned.xlsx`.
- **SCS-PI** (Paracel Is.): the ad-hoc `PI*` ISO code cleared (no ISO code —
  disputed status), `Solved = Yes`, revision note trimmed to "political
  status is disputed."
- **TCS-AB** (Abkhaziya, issue #5) and **TCS-NK** (Nagorno Karabakh, issue
  #13): both marked `Solved = Yes`.

These edits — plus the valid-units-only filtering and the SDS column
renaming — have now been propagated to the production tables one level up
(`../Level1_21-Aug-26.xlsx`, `../Level2_21-Aug-26.xlsx`,
`../Level3_20-Aug-26.xlsx`, `../Level4_20-Aug-26.xlsx`,
`../CHANGES_20-Aug-26.xlsx`), which previously still used the old column
names and still carried the 386 blank padding rows and `SUD-OO`. Production
and this Towards_SDS staging set are now content-identical. Row order in
every rebuilt file matches each table's original order — the master's own
row order (which the 25-08-26 edit left in a partial, seemingly accidental
sort) was **not** carried over; edits were matched back onto the original
order by `(Level, Local_name)` key.

## Deliberately NOT done here (open questions for the 25 Aug call)

- **ISO code format (alpha-2 vs. alpha-3).** Serge's older mapping equated
  terrestrial's ISOcode to marine's alpha-2 code; the newer shapefile-based
  scheme uses `ISO_ter1` = alpha-3. Terrestrial's current ISOcode values
  have not been re-verified against either format.
- **Overlapping-claim ISO columns (`ISO_ter2`, `ISO_ter3`).** Not added here
  pending a decision on scope.
- **Marine-only columns not yet mirrored in terrestrial**: `type`, `source`,
  `external url`. Not added here.
- **Definitions are still empty** in every table.

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
