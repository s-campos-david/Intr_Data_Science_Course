# Predicting Bottom Performers on Maternal Morbidity

Supervised machine learning project for the **Introduction to Data Science and Supervised Machine
Learning** course on Udacity.

## Motivation

Every year CMS publishes a Maternal Health file covering every Medicare-certified hospital in the US.
One of the measures in it, PC_07a, is the risk adjusted rate of severe obstetric complications, and
CMS labels each hospital as a **Top**, **Middle** or **Bottom Performer** on it.

The catch is that this label only exists for hospitals with enough delivery volume to be scored.
Almost half of the hospitals in the file (2,295 out of 4,655) have no label at all.

So I wanted to know: **can we predict that a hospital is a Bottom Performer using only information
that does not come from the complication measure itself?** If it works even partially, it gives a
cheap way to build a watch list, including for the hospitals CMS cannot score.

Three questions guided the analysis:

1. What does a hospital with poor maternal outcomes look like? The big ones, the small ones, the ones
   doing a lot of C-sections?
2. Does the self reported maternal safety structural measure (SM_7) say anything about actual
   outcomes?
3. Can a model flag likely Bottom Performers well enough to be useful as a screening tool?

The project follows the **CRISP-DM** process: business understanding, data understanding, data
preparation, modeling, evaluation and a short deployment example.

## Summary of results

**1. Geography matters far more than anything measured at the hospital.** The share of a state's
hospitals labelled Bottom Performer runs from around 8% (Ohio, Kansas) to 51% (New York). That is a
difference of more than six times, much wider than anything the hospital level columns show. After
geography, a high cesarean birth rate pushes the risk from 21.5% in the lowest quintile to 32.5% in
the highest, and the largest hospitals are the worst volume group. Hospitals with a university in the
name are labelled Bottom Performer 45% of the time against 24% for everyone else, which is most
likely high risk case mix rather than worse care.

**2. The structural measure predicts nothing.** 25.1% of hospitals that attested *Yes* to SM_7 are
Bottom Performers, against 24.7% of those that did not. The attestation and the outcome are
unrelated in this data. A negative result, but a useful one.

**3. The model works, but it is weak.** Three algorithms were compared against a dummy baseline using
5-fold cross validated ROC AUC:

| Model | CV ROC AUC |
| --- | --- |
| Random Forest | 0.624 |
| Logistic Regression | 0.616 |
| Gradient Boosting | 0.591 |
| Dummy (always "not bottom") | 0.500 |

On the held out test set the Random Forest scores **0.629 ROC AUC**, catching **56% of the real
Bottom Performers at 35% precision**. For comparison, the dummy model reaches 75% accuracy while
catching none of them, which is exactly why accuracy is the wrong headline metric for a 25/75 class
split.

That is good enough to rank a shortlist and not good enough to make a decision. The main limitation
is the data, not the model: the file has nothing about staffing, teaching status, payer mix or
patient demographics, and those are the things that actually drive maternal outcomes.

## Files in this repository

| File | Description |
| --- | --- |
| `maternal_health_analysis.ipynb` | The full analysis. EDA, cleaning, feature engineering, model comparison, evaluation and a worked prediction scenario. All outputs are saved so it renders on GitHub without running it. |
| `data/Maternal_Health-Hospital.csv` | The source dataset from CMS. 17,945 rows, 4,655 hospitals, 4 measures each, reporting period 01/01/2025 to 12/31/2025. |
| `data/HOSPITAL_Data_Dictionary.pdf` | The official CMS data dictionary for the whole Hospital Downloadable Database. The Maternal Health section describes the measures used here. |
| `images/` | The 10 charts produced by the notebook, exported as PNG for the blog post. |
| `requirements.txt` | Python packages needed to run the notebook. |

## Libraries used

- **pandas** - loading and reshaping the data, the file is in long format and has to be pivoted
- **numpy** - log transforms and array work
- **matplotlib** and **seaborn** - all the charts
- **scikit-learn** - preprocessing pipelines, the four models, cross validation, evaluation metrics
  and permutation importance
- **jupyter** - the notebook itself

Developed on Python 3.12 with pandas 3.1, numpy 2.5, scikit-learn 1.9, matplotlib 3.11 and
seaborn 0.13.

## How to run it

```bash
pip install -r requirements.txt
jupyter notebook maternal_health_analysis.ipynb
```

Run the cells in order. The notebook reads `data/Maternal_Health-Hospital.csv` with a relative path,
so launch Jupyter from the repository root. Everything is seeded with `random_state=42`, so the
numbers above are reproducible.

## Notes on the data

A few things that are worth knowing before touching this file:

- It is in **long format**. One row is one hospital and one measure, so every hospital appears four
  times.
- Missing values are **not blank**. CMS writes them as the strings `Not Available` and
  `Not Applicable`, which means every numeric column loads as text. The `Footnote` column explains
  why a value is missing (code 5 is "not available for this reporting period", code 1 is "too few
  cases to report").
- `smm_rate` (the PC_07a score) and the performance tier are **the same information**. The tier is
  just a threshold on the score, and the ranges do not overlap at all. Using the score as a feature
  would leak the target, so it is dropped, along with PC_07b which is the same measure under a
  slightly different definition.

## Blog post

A non technical write up of the findings lives on the project's GitHub Pages site:
<https://s-campos-david.github.io/Intr_Data_Science_Course/>

## Acknowledgements

- Data from the **Centers for Medicare & Medicaid Services**, Hospital Downloadable Database,
  Maternal Health - Hospital dataset, available on the
  [Provider Data Catalog](https://data.cms.gov/provider-data/). As a work of the U.S. government this
  data is in the public domain. CMS does not endorse this analysis.
- Measure definitions taken from the CMS Downloadable Database Data Dictionary included in `data/`.
- Project brief and the CRISP-DM framing come from the Udacity **Introduction to Data Science and
  Supervised Machine Learning** course.
