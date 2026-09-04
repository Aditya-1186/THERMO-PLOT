# Cp-T Explorer

An interactive dashboard for exploring how the constant-pressure specific
heat capacity (Cp) of materials varies with temperature (T), built with
Streamlit and Plotly.

**Coverage:** 330 condensed-phase materials (metals & alloys, ceramics,
refractories, semiconductors, and other technologically relevant compounds),
1,040 fitted temperature intervals.

## Features

- Category filter and text search over material name / chemical symbol
- Multi-select plotting of up to 12 materials at once, with a shared,
  interactive Plotly chart (zoom, pan, hover cursor values, PNG export)
- User-defined temperature window (slider), with an automatic warning and
  clipping when the requested range falls outside a material's valid range
- Phase-transition markers where a material switches Shomate interval
  (e.g. solid → liquid)
- Molar (J·mol⁻¹·K⁻¹) or mass-specific (J·g⁻¹·K⁻¹) Cp display
- Point-comparison table + bar chart: Cp of all selected materials at one
  chosen temperature
- Full database browser with CSV export
- Per-material detail cards: molecular weight, valid range, fit quality,
  reference code, and source notes

## Data & method

Cp(T) is computed from a Shomate-style 4-constant polynomial:

```
Cp°(T) = A + B·t + C·t² + D·t³        (t = T[K] / 1000, Cp in J·mol⁻¹·K⁻¹)
```

The A–D coefficients in `data/shomate_intervals.csv` were derived in-house
by least-squares fitting this 4-term polynomial to Cp(T) values generated
from the 7-coefficient NASA Glenn polynomial, sampled at 80 points per
interval. Where a single fit could not keep the maximum relative error
below 4% across a material's full range, the interval was split (up to 4
levels deep, 25 K minimum width) and re-fit — the same reason NIST's own
Shomate tables list multiple T-windows for a single species.

**Primary source:** B. J. McBride, M. J. Zehe, S. Gordon, *NASA Glenn
Coefficients for Calculating Thermodynamic Properties of Individual
Species*, NASA/TP-2002-211556, September 2002, Appendix D (condensed-phase
species).

**Citation note:** the A–D coefficients are a derived least-squares fit to
the NASA source above, not an independently published Shomate table. Cite
both the primary NASA reference and this derivation when using the dataset.

## Project structure

```
cp-explorer/
├── app.py                        # Streamlit app
├── requirements.txt
├── data/
│   ├── shomate_intervals.csv     # one row per fitted T-interval (used for plotting)
│   └── material_summary.csv      # one row per material (used for browsing/search)
├── .streamlit/config.toml        # theme
└── README.md
```

## Run locally

```bash
pip install -r requirements.txt
streamlit run app.py
```

## Deploy on Streamlit Community Cloud

1. Push this folder to a new GitHub repository (keep the folder structure
   as-is — `data/` must sit next to `app.py`).
2. Go to https://share.streamlit.io, click **New app**, and point it at
   your repo, branch `main`, main file `app.py`.
3. Deploy. No secrets or extra configuration are required.

## Extending the dataset

This build ships with 330 real, cited materials (comfortably above a
200-material minimum) but does **not** include Polymers, Glasses, or
Composites. To extend coverage, add rows to `data/shomate_intervals.csv`
and `data/material_summary.csv` using the same column layout, sourced from
a citable database (NIST Chemistry WebBook, NIST-JANAF, Materials Project,
AFLOW, OQMD, PoLyInfo, MatWeb, AZoM, COD, PubChem, etc.), and record the
source in the `Reference Code` / `Source / Notes` columns.
