# Predicting Bottom Performers on Maternal Morbidity

Project for the Udacity **Introduction to Data Science and Supervised Machine Learning** course.

## Motivation

CMS publishes a Maternal Health file for every Medicare-certified hospital in the US. One of the
measures, PC_07a, is the risk adjusted rate of severe obstetric complications, and CMS labels each
hospital Top, Middle or Bottom Performer on it.

The label only exists for hospitals with enough deliveries to be scored. 2,295 of the 4,655
hospitals in the file have no label at all.

So I wanted to know whether a hospital can be identified as a likely Bottom Performer **without
using the complication measure itself**. If it works, it gives a cheap way to build a watch list,
including for hospitals CMS cannot score.

Three questions:

1. What does a hospital with poor maternal outcomes look like?
2. Does the self reported maternal safety structural measure (SM_7) say anything about outcomes?
3. Can a model flag likely Bottom Performers well enough to be useful?

The project follows CRISP-DM.

## Results

**1. Geography is the widest pattern.** The share of a state's hospitals labelled Bottom Performer
runs from around 8% (Kansas, Ohio) to 51% (New York), more than six times. Nothing measured at the
hospital comes close. After that, a high cesarean rate moves the risk from 21.5% in the lowest
quintile to 32.5% in the highest, and university hospitals are at 44.6% against 24.3% for everyone
else, which most likely reflects high risk case mix rather than worse care. Delivery volume has a U
shape: the safest group is mid sized hospitals at 20.8%, the largest are the worst at 31.1%.

**2. The structural measure predicts nothing.** 25.1% of hospitals that attested *Yes* to SM_7 are
Bottom Performers, against 24.7% of those that did not. Unrelated, so I dropped it from the model.

**3. The model is weak but real.** Features: state, cesarean rate, log delivery volume, and a flag
for a university in the name. Cross validated ROC AUC on the training set, averaged over 5 fold
seeds:

| Model | CV ROC AUC |
| --- | --- |
| Random forest | 0.617 |
| Logistic regression | 0.612 |
| Dummy (always "not bottom") | 0.500 |

The forest is 0.005 ahead, but each model swings by about 0.02 just from reshuffling the folds, so
the gap is inside the noise and the two are tied. I went with the logistic regression because it is
simpler and the coefficients are readable.

On the held out test set it gets **ROC AUC 0.623**, recall **0.514** and precision **0.319**. The
dummy gets 0.749 accuracy with zero recall, which is why accuracy is the wrong headline metric on a
25/75 split.

Good enough to order a shortlist. Not good enough to decide anything. The limit is the data, not the
model: the file has nothing on staffing, teaching status, payer mix or patient demographics.

## Files

| File | What it is |
| --- | --- |
| `maternal_health_analysis.ipynb` | The analysis. EDA, cleaning, feature work, model comparison, evaluation and a worked prediction. Outputs are saved so it renders on GitHub without running. |
| `data/Maternal_Health-Hospital.csv` | Source data from CMS. 17,945 rows, 4,655 hospitals, 4 measures each, period 01/01/2025 to 12/31/2025. |
| `data/HOSPITAL_Data_Dictionary.pdf` | Official CMS data dictionary. The Maternal Health section covers the measures used here. |
| `images/` | The six charts from the notebook, exported for the blog post. |
| `requirements.txt` | Packages needed to run the notebook. |

## Libraries

- **pandas** - loading and pivoting the file, which arrives in long format
- **numpy** - log transform and array work
- **matplotlib** / **seaborn** - the charts
- **scikit-learn** - pipelines, the models, cross validation, metrics, permutation importance
- **jupyter** - the notebook

Built on Python 3.12 with pandas 3.1, numpy 2.5, scikit-learn 1.9, matplotlib 3.11, seaborn 0.13.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook maternal_health_analysis.ipynb
```

Run the cells in order, and start Jupyter from the repository root because the data path is
relative. Everything uses `random_state=42`, so the numbers above reproduce.

## Notes on the data

Three things worth knowing before using this file:

- It is **long format**. One row is one hospital and one measure, so each hospital appears four
  times.
- Missing values are **not blank**. CMS writes them as `Not Available` and `Not Applicable`, so
  every numeric column loads as text. The `Footnote` column says why a value is missing.
- `smm_rate` (the PC_07a score) **is** the label. The tier is a threshold on it and the ranges do not
  overlap at all, so using it as a feature would leak the target. Dropped, along with PC_07b which is
  the same measure under a slightly different definition.

One caveat I did not fully solve: 33 hospitals report a cesarean rate of 0% or above 60%, a few at
97-100%. For low risk first births those are not credible and look like reporting errors. The
cesarean pattern still holds when I restrict to hospitals with at least 200 NTSV births, so I left
the rows in and flagged it rather than filtering.

## Use of AI tools

I used **Snowflake Cortex Code** on this project. Being specific about what that covered, since the
Udacity Honor Code asks for accurate disclosure:

**Where I used it**

- Brainstorming the framing. I had the dataset and knew I wanted a supervised problem; discussing
  candidate targets is how I landed on Bottom Performer as a binary label instead of the three class
  version.
- Repository structure and the first draft of the notebook scaffolding and the write ups.
- Code review, and getting the local Python environment working. The official installer is blocked
  on my machine, so the toolchain came from the NuGet CPython package.
- Reviewing my prose for clarity.

**What I did myself**

- Deciding what the project would be and which questions to ask.
- The modelling calls that matter: dropping `smm_rate` and `smm_rate_nobt` as leakage, pooling rare
  states, excluding SM_7 after it came out flat, and rejecting the children's / women's / health
  system / faith based name flags and the NTSV share feature after they failed to move CV AUC.
- Catching that the single split made the random forest look better than it is, and asking for the
  repeated seed comparison that showed the two models are tied. The decision to prefer the simpler
  logistic regression is mine.
- Noticing the implausible cesarean values and running the denominator robustness check.
- Running everything, checking the output, and the final review of every number in this README, the
  notebook and the blog post.

**Where the line is.** The analysis decisions, the interpretation and the conclusions are mine, and I
can defend every one of them. I treated the AI as a reviewer and a pair programmer, not as the
author. Anything it drafted that I could not explain or did not agree with, I changed or removed.

## Acknowledgements

- Data from the **Centers for Medicare & Medicaid Services**, Hospital Downloadable Database,
  Maternal Health - Hospital dataset, from the
  [Provider Data Catalog](https://data.cms.gov/provider-data/). As a work of the U.S. government it
  is in the public domain. CMS does not endorse this analysis.
- Measure definitions from the CMS data dictionary included in `data/`.
- Project brief and the CRISP-DM framing from the Udacity course.

## Blog post

Non technical write up: <https://s-campos-david.github.io/Intr_Data_Science_Course/>
