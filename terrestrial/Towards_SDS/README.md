# Towards SDS — Terrestrial/Marine column harmonization (staging area)

This folder is a **staging area**, not the production tables. `Level1_SDS-aligned.xlsx`
through `Level4_SDS-aligned.xlsx` are copies of the current live tables
(`../Level1_21-Aug-26.xlsx` … `../Level4_20-Aug-26.xlsx`) with column headers
renamed to move toward SKOS/SDS terminology and toward alignment with the
marine domain's column naming, ahead of the terrestrial/marine harmonization
call (Serge Gofas & Paco Pando, 25 Aug 2026, 09:00 CEST). No row data was
changed — only header text. The live/production files are untouched.

`Terrestrial_Master_SDS-aligned.xlsx` additionally consolidates all four
levels into a single table (1,434 rows), for the part of the "one table per
level vs. one table for everything" question that's already settled: a
single master table *per domain* (i.e. one for terrestrial, independently
of whatever the marine side does, and independently of whether terrestrial
and marine end up as one SDS standard or two — both still open).

## Context

Prompted by Serge Gofas's email of 23 Aug 2026 ("Re: Getting closer to
finalising Geoschemes"), which maps the terrestrial columns onto the marine
domain's proposed SDS-oriented scheme, and onto the columns of Marine
Regions' own shapefile attribute table (the latest, most authoritative
source for the marine side, since the previously posted marine Excel needs
to be rebuilt from that shapefile export).

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
present under that name and needed no change — it already matches marine's
`definition` column and SDS's `rdfs:comment` / `skos:definition`.

## Consolidated master table

`Terrestrial_Master_SDS-aligned.xlsx` merges the four renamed tables above
into one sheet (`tblGeoschemesTerrestrial`), with two columns replacing the
per-level bookkeeping:

- **`Level`** (1–4) — which of the original tables the row came from.
- **`skos_broader`** — the immediate parent's `Local_name` code, replacing
  the level-specific `Level_1` / `Level_2` / `Level_3` parent-reference
  columns (which meant different things depending on which file they were
  in — now that `Level` carries that information, one generically-named
  parent column works for every row). Blank for Level 1 (continents have no
  parent).

Every other column (`Local_name`, `Full_name`, `ISO_ter1`, `In Ed. 2`,
`Definition`, `Change according to page 9`, `Solved`, `Doubt`, `ISSUES`,
`Id`) carries straight across, populated where the source table had it and
blank where it didn't (e.g. `Solved`/`Doubt`/`ISSUES` are blank for Level
1–2 rows, since those tables never carried them). Level 1's old `Exchange
notes` column and Level 3/4's `Revision notes` column are folded into one
shared `Revision notes` column, the same treatment `Revision notes` already
got within Level 4 earlier in this process. Verified against the four
source tables cell-by-cell — 1,434 rows, zero mismatches.

This does **not** decide whether terrestrial and marine end up as one SDS
standard or two, or what marine's own table structure should be — both are
still explicitly on the agenda for the 25 Aug call. It only settles that
*within* the terrestrial domain, the four levels live in one table now
instead of four.

## Deliberately NOT done here (open questions for the 25 Aug call)

- **ISO code format (alpha-2 vs. alpha-3).** Serge's older mapping equated
  terrestrial's ISOcode to marine's alpha-2 code; the newer shapefile-based
  scheme uses `ISO_ter1` = alpha-3. Terrestrial's current ISOcode values
  have not been re-verified against either format — worth confirming on
  the call before treating `ISO_ter1` as authoritative.
- **Overlapping-claim ISO columns (`ISO_ter2`, `ISO_ter3`).** Marine needs
  these for disputed sea areas (Falklands, Kuriles, South China Sea, etc.).
  Terrestrial has not historically distinguished such cases (aside from
  Cyprus, Donbas, etc.) — not added here pending a decision on scope.
  Bart's MRGID suggestion is for **Definition** in the marine domain, not for
  disambiguating codes.
- **Marine-only columns not yet mirrored in terrestrial**: `type`, `source`,
  `external url`. Not added here — no terrestrial data exists for them yet,
  and adding empty columns without content seemed premature.
- **Definitions are still empty.** The `Definition` column exists but has
  no content in any of the four tables. The email proposes Definitions
  point to a landing page (Zenodo and/or Marine Regions, in words, in
  English) — worth agreeing on the exact target/wording pattern on the
  call before populating at scale.

## Files in this folder

- `Level1_SDS-aligned.xlsx` (9 units)
- `Level2_SDS-aligned.xlsx` (52 units)
- `Level3_SDS-aligned.xlsx` (369 units)
- `Level4_SDS-aligned.xlsx` (1,004 units)
- `Terrestrial_Master_SDS-aligned.xlsx` (all 1,434 units in one table)
- This `README.md`
