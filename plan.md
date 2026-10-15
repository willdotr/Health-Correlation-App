# Plan: NHANES Health Correlation Explorer (`app.py`)

## Context
M9 exercise: build a single-file Streamlit app that explores correlations in `nhanes_health.csv` (4,433 adults, 11 columns: age, sex, bmi, waist_cm, systolic_bp, diastolic_bp, hba1c, total_chol, hdl_chol, sleep_hours, sedentary_minutes). Read the CSV: header + 4,433 rows, `sex` is the only text column, no empty fields found. The numbers must be verifiable against pandas (`verification.md`). Project folder currently holds only the CSV and empty `plan.md`/`prompts.md`/`verification.md` stubs. `uv` is available; `python` is not on PATH, so run via `uv run`.

## Design
Single file `c:\Users\will\Documents\dev\data science week 9 code\app.py`; packages: streamlit, pandas, plotly (scatter/heatmap/trendline via numpy, no statsmodels or scipy). Load CSV with relative path `nhanes_health.csv` via `@st.cache_data`.

Numeric columns = all except `sex`. `sex` is never offered in correlation dropdowns/heatmap picker.

**Single filter pipeline (the key rule):** sidebar widgets (sex: All/Female/Male; age range slider 20–80, default full) produce one `filtered` DataFrame. Every view reads only `filtered` — never the raw df.

**Correlation helper** `corr_with_n(df, cols, method)`: drops rows with NaN in the involved columns (pairwise complete), returns r and n; uses `df[a].corr(df[b], method=...)`, so Pearson/Spearman matches the pandas check. Heatmap uses `filtered[cols].corr(method=...)` (pairwise-complete) plus a matching n matrix; show n in hover and a caption (min n).

## Required features (explicit)
- **Filters:** sidebar controls for **sex** (All/Female/Male) and an **age range** slider. They update *every* view: the data table, scatter plot, r, n, and heatmap (all read the one `filtered` DataFrame).
- **Correlation method:** a Pearson / Spearman toggle (`st.sidebar.radio`) that applies to *both* the pairwise view (r) and the heatmap (all coefficients), through the same `method` variable passed to `corr_with_n` and the heatmap `.corr(method=...)`.

## Checklist (all must hold)
- [x] All five features: data preview, pairwise view, heatmap, sidebar filters, Pearson/Spearman toggle.
- [x] r and n computed from the **filtered** data in every view (no view touches the raw df).
- [x] `sex` left out of the correlations (excluded from `NUMERIC_COLS`; never in dropdowns or heatmap picker).
- [x] Heatmap color scale fixed to −1…1 (`zmin=-1, zmax=1`, diverging, centered at 0); every cell shows its coefficient.
- [x] **n shown next to every r:** pairwise `st.metric` r beside n; each heatmap cell labeled "r" with "n=…" (cell text and hover).
- [x] Built a little at a time, starting with data and filters (stages below; run and check after each).
- [x] Only allowed packages: Python, Streamlit, pandas, plotly (numpy ships with pandas). No scipy or others without asking; Spearman uses pandas' `.corr(method="spearman")`. `requirements.txt`: streamlit, pandas, plotly.

## Build order (a little at a time)
1. Load data + sidebar filters + sidebar method toggle (Pearson/Spearman); show filtered n.
2. **Data preview:** dataset description (NHANES 2017–2018, adults 20+, age 80 = 80+, sample not population) + `st.dataframe(filtered)`.
3. **Pairwise view:** X and Y selectboxes (default age, systolic_bp); r and n via `st.metric`; plotly scatter with OLS trend line (np.polyfit on the valid rows; for Spearman note that the line is linear fit); checkbox "color by threshold" using a per-variable threshold map (bmi ≥30, SBP ≥130, DBP ≥80, hba1c ≥6.5, total_chol ≥240; HDL/others: no threshold → option disabled or user-entered). Warning if n < 3 or a variable is constant. Caption: r near 0 ≠ no relationship (U-shaped sleep example).
4. **Heatmap:** `st.multiselect` of numeric vars (default all, require ≥2); `px.imshow` with `zmin=-1, zmax=1`, diverging scale centered at 0, every cell annotated with its coefficient (`text_auto=".2f"`); caption about fixed −1…1 scale and n.
5. Empty-filter handling (`st.stop()` with message), tabs/sections for layout.
6. `requirements.txt` (streamlit, pandas, plotly) after the app works.

Run: `uv run --with streamlit,pandas,plotly streamlit run app.py`

## Unclear points / assumptions to confirm
- Plotly vs seaborn: plan uses plotly (interactive hover, fixed color scale easy).
- Missing values: none seen in the CSV, but code handles them pairwise and shows n anyway.
- Age slider max 80 since 80 means "80 or older".

## Verification (user does the pandas checks, per `verification.md`)
Run the six checks from the exercise: r for age/systolic_bp, bmi/waist_cm, waist_cm/hdl_chol (all); r and n for age/systolic_bp Female-only and Male-only; heatmap square under the Female filter equals check 4. Also toggle Spearman and confirm app r equals `df[a].corr(df[b], method="spearman")`, and confirm sex change updates table, scatter, r, n and heatmap.