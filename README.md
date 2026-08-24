# Prateung Farm 2569

Herd evaluation for Prateung Farm, **January to July 2569 (2026)** — 735 individual milk tests
across seven months, assembled from 34 raw source files into one auditable dataset.

Two versions live here. The page opens on the legacy dashboard; the tab bar at the top
switches between them.

| | What it is |
|---|---|
| **Legacy v7.4** | The landing view. The earlier dashboard, kept for comparison. Several of its figures are known to be wrong — see below. |
| **Dashboard 2569** | Current. Every figure traces back to a row in a source file. |

## Why the legacy version is kept

Not out of sentiment. It is the thing the current dashboard is arguing against, and the
disagreements are the point.

The largest is **L01**: v7.4 joins the TDRC laboratory files to the farm register on the
ID number. That matches 93 cows and gets **every single name wrong**, so its per-cow lab
values are attached to the wrong animals. The current dashboard joins on cow name instead
and carries a `join_method` column so the join can be checked.

Read anything from v7.4 alongside the **Data & limitations** tab of the current dashboard
before quoting it.

The legacy page also loads Chart.js and Google Fonts from a CDN, so it needs a connection.
The current dashboard inlines everything and works offline.

## What is in the current dashboard

Seven tabs: Overview, Milk quality, Acidosis, MUN × protein, Feed, Reproduction, and
Data & limitations.

A few things it deliberately does differently from a typical herd report:

- **A low fat-to-protein ratio is split before it is read.** Fat falling and protein rising
  produce the same ratio and mean different things, so the acidosis tab separates them.
- **The F/P ratio can be restricted to cows at 100 days in milk or less**, where it still
  tracks the ration rather than the stage of lactation. Restricted that way, the herd median
  never falls below 1.0 in any month.
- **Milk from an inflamed udder is excluded from every fat-based figure.** A test with SCC
  over 500 and fat over 4.0% is mastitis, not ration. Four tests are dropped and all four
  are listed by name.
- **Sampling is shown, not assumed.** The laboratory tests roughly a third of the herd each
  month, and not the same third. Pen 3 holds 18 cows in every month and was sampled 12, 8,
  4, 1, then none — its line disappearing from a chart is a sampling gap, not an empty pen.
- **A denominator under 20 is reported as a count, never as a rate.**
- **21 limitations are recorded in full**, each with what was found, what it does to the
  conclusions, and the workaround.

## Layout

```
index.html       tab shell; loads either dashboard in a frame
dashboard.html   the current dashboard, fully self-contained
legacy.html      Farm Prateung v7.4, needs a CDN connection
favicon.png
```

Both dashboards are complete HTML documents with their own styles and scripts, so the shell
loads them in a frame rather than inlining them. Each also works on its own URL:
`legacy.html`, `dashboard.html`. Linking to `#current` opens the shell on the current
dashboard, `#legacy` on the legacy one.

The frame URL carries a `?v=` stamp — a hash of the file it points at, rewritten on every
deploy. Both dashboards are republished at the same filenames and Pages serves HTML with a
ten-minute max-age, so without it a browser holding the old copy goes on showing it, and
inside a frame there is nothing for the reader to reload. The stamp changes when the file
changes and never otherwise, so an unchanged file still comes from cache.

## Where the numbers come from

Everything derives from `master/Prateung_Farm_2569_Master.xlsx`, which is rebuilt from the
34 raw files by a re-runnable script. Every data row carries a source id, its source sheet
and its row number.

```
34 raw files
  └─ build_prateung_master.py  →  Prateung_Farm_2569_Master.xlsx
       └─ build.py             →  dash.json
            └─ assemble.py     →  dashboard.html
```

Raw sources: 6 monthly workbooks, 7 TDRC milk analyses, 7 bulk-tank PDFs, 4 feed-laboratory
PDFs, 3 culture PDFs, 7 duplicate milk-composition PDFs.

The build scripts and the workbook are not in this repository — it holds the published
pages. Any figure quoted elsewhere should travel with its **n** and its **date**.

## Thresholds

Single constants, so moving one moves every label built from it.

| Constant | Value | What it governs |
|---|---|---|
| `FAT_LOW` | 3.35% | Depressed milk fat, and the fat standard on the composition chart |
| `DIM_MAX` | 100 days | The window where the F/P ratio is worth reading |
| `SCC_MAX` / `FAT_HI` | 500 / 4.0% | Mastitic milk, excluded from fat-based figures |

## Known gaps

- **July has no monthly workbook.** Milk quality and one bulk-tank result survive; every
  other KPI is uncomputable for that month, and it has no days-in-milk and no pen record.
- **June and July pen assignments are carried forward** from the last month on record rather
  than observed. The tables show how many.
- **No per-cow body weight exists for 2569.** The pen body weights used for dry-matter
  intake are typed into the ration sheet and frozen at 556 / 578 / 582 / 493 kg across all
  six months, so intake as a share of body weight is only as good as that divisor.
- **Reproduction data is unusable for January** and absent for July.
- **July's peNDF was reported by the farm, not read off a sheet.** The farm sieved in
  July and gave the result — 46.88% on the top two screens for the high-yield mix, 25.17%
  peNDF outright for the low-yield one — but the run itself is in none of the 34 files.
  Both are used as given and labelled *supplied*. Neither came with the individual screens,
  so July has no peNDF > 1.18 mm, and the peNDF card shows that one month alone.

## Status

For academic discussion and presentation. Not for sale or redistribution.
