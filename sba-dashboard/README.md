# SBA Franchise Default Rates: dashboard copy

A copy of the Claude dashboard artifact
https://claude.ai/artifact/JU4ZeBrx6decSBCj6eVTTq
saved on 2026-11-04 (page version 11, data as of 2026-11-03).

## Contents

- `index.html`: the dashboard page (the HTML, CSS and JavaScript the artifact runs). It is a fragment, not a full document: the artifact platform supplies the surrounding page and the `dash` runtime.
- `store/dash/meta.json`: dashboard title.
- `store/dash/params.json`: saved filter state (`with90`: include 1990s loans).
- `store/datasets/*.json`: metadata for the 10 datasets the page reads (title, description, source file name, updated stamp). Each dataset points to a CSV in the artifact's file store, which is **not** included here.
- `store/files/index.html.json`: the same page HTML wrapped in JSON, as stored in the artifact's database.

## Not included

The dataset CSVs (`all_industries.csv`, `baselines.csv`, `brands.csv`, etc.) are fixed files attached to the artifact. They are derived from the SBA FOIA loan files in `SBA-Data/`. To rebuild them, rerun the analysis against those files.
