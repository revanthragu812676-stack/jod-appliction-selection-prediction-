# Job Applicant Selection — Frontend-Only Conversion

This is a full conversion of the uploaded `Job_Applicant_Selection_Prediction_Sklearn`
Streamlit/scikit-learn project into a React + Vite + Tailwind app that runs
**entirely in the browser**. No Python runtime, no Streamlit, no joblib, no
Flask, no backend, no database remain anywhere in the shipped app.

## What changed, and what didn't

**Unchanged (same ML logic, same features, same data):**
- `ColumnTransformer(OneHotEncoder on education, numeric passthrough)` →
  `RandomForestClassifier(n_estimators=200, random_state=42, class_weight="balanced")`
- `train_test_split(test_size=0.2, random_state=42, stratify=y)`
- Features: experience_years, education, skills_score, interview_score,
  aptitude_score, certifications, internship, projects
- Target: `selected`
- The original `job_applicants.csv`, copied in unchanged
- The 4 candidate personas from the original Streamlit `app.py`, values
  copied exactly (Custom Candidate, Top Engineering Lead, High-Aptitude
  Graduate, Under-Skilled Applicant)
- The composite score formula (`skills*0.4 + interview*0.4 + aptitude*0.2`)
  and the 5 competency checks, copied from the original app

**Converted:**
- The `.pkl` is **not used directly** — a scikit-learn pickle can't be
  loaded or executed in a browser at all. `ml/train.py` reruns the original
  `train_model.py`'s exact training code (same parameters, same data, same
  split) once, offline, and exports the resulting 200 fitted trees, the
  OneHotEncoder's category list, and every metric to `src/ml/model.json`.
- `src/ml/model.js` is a from-scratch JavaScript reimplementation of
  `RandomForestClassifier.predict_proba`: it rebuilds the exact
  `ColumnTransformer` column order (OneHotEncoder columns first, then the 7
  numeric features — this order matters), walks each of the 200 exported
  trees with the same `<= threshold` rule scikit-learn uses internally, and
  averages each tree's leaf class-probability vector. `Math.fround` mirrors
  scikit-learn's internal float32 casting.

## How close is the JavaScript prediction to the original?

Exact. Checked against `pipeline.predict_proba()` from the original Python
pipeline on all 500 rows of the source CSV: **0 difference** in predicted
probability on every row. All four personas were also cross-checked and
match the original model exactly:

| Persona | Prediction | Probability |
|---|---|---|
| Custom Candidate | Selected | 59.5% |
| Top Engineering Lead | Selected | 99.0% |
| High-Aptitude Graduate | Not Selected | 44.5% (below 50%) |
| Under-Skilled Applicant | Not Selected | 4.5% |

(The High-Aptitude Graduate persona predicting "Not Selected" is the real
model's real output — heavy portfolio and aptitude don't fully offset only
1 year of experience in this dataset. Not an error, not softened.)

## Real results (100-row test set)

| Metric | Value |
|---|---|
| Accuracy | 80.0% |
| Precision (macro) | 78.0% |
| Recall (macro) | 77.5% |
| F1 (macro) | 78.0% |

Every number, the confusion matrix, and the feature importances on the
Model Performance page are the real output of retraining the original code
— nothing is invented.

## Running

```bash
npm install
npm run dev        # local dev server
npm run build       # output in dist/
```

## Netlify

- Build command: `npm run build`
- Publish directory: `dist`

`netlify.toml` sets both, plus a catch-all SPA redirect (`/* → /index.html`,
status 200) so refreshing `/evaluate`, `/dataset`, or `/performance`
directly works — this app uses React Router's `BrowserRouter`, so that
redirect is required (and included).

## Final testing performed

- **CSV loading** — `public/job_applicants.csv` downloads correctly from
  the Dataset page; `src/ml/model.json` carries the precomputed sample rows,
  stats and chart data so the app never needs to parse the CSV at runtime.
- **Applicant prediction** — tested against the original model's `predict.py`
  logic; matches exactly.
- **Presets** — all 4 personas tested; predictions match the original
  Streamlit app's behavior exactly (table above).
- **Charts** — Dashboard (skills/experience/education vs. selection rate)
  and Performance (confusion matrix, feature importance) render correctly.
- **Mobile layout** — grids collapse to a single column below the `sm`
  breakpoint; nav wraps.
- **Production build** — `npm run build` succeeds with no errors.
- **No Python dependency remains** — `ml/train.py` is a one-time, offline
  export script (like a build step run once and committed); nothing in
  `src/` or the shipped `dist/` imports, fetches, or executes Python,
  Streamlit, or joblib.

## Pages

1. **Dashboard** — total applicants, selection rate, average skills score, model accuracy, skills/experience/education vs. selection charts
2. **Applicant Evaluation** — persona presets, all 8 model inputs, Evaluate Applicant button, selection prediction + probability + competency summary
3. **Dataset** — first 50 rows, search, total applicants, selection rate, average skills score
4. **Model Performance** — accuracy, classification results, confusion matrix, feature importance

## Structure

```
job-applicant-selection/
├── public/
│   └── job_applicants.csv
├── ml/
│   ├── train.py                  one-time offline export (not shipped)
│   └── job_applicants.csv        copy used to run train.py
├── src/
│   ├── ml/
│   │   ├── model.js               forest walker, encoder, personas, competency logic
│   │   └── model.json             fitted forest + real metrics (bundled)
│   ├── pages/
│   │   ├── Dashboard.jsx
│   │   ├── Evaluation.jsx
│   │   ├── Dataset.jsx
│   │   └── Performance.jsx
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── package.json
├── netlify.toml
└── README.md
```

## Retraining (only if you have a different/updated CSV)

```bash
pip install pandas scikit-learn numpy
python ml/train.py   # writes src/ml/model.json
```
