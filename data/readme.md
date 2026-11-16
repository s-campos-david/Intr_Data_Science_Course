This is the readme file.

## Libraries
Python libraries used:
- Pandas: used to read the csv file and to apply transformations, as to pivot a table, to group data, etc
- numpy: Used for mathematic calculations and transformations.
- matplotlib: Used to generate graphs.
- seaborn: Used to build histograms and other graphs additionally to matplotlib.
- scikit-learn: Used to build the pipelines, the models themselves, the cross validation, the metrics and the reads, the heart of the project.

Scikit-learn imports used to build the model:

model_selection   train_test_split, cross_val_score, StratifiedKFold
pipeline          Pipeline
compose           ColumnTransformer
preprocessing     StandardScaler, OneHotEncoder
impute            SimpleImputer
linear_model      LogisticRegression
ensemble          RandomForestClassifier
dummy             DummyClassifier
inspection        permutation_importance
metrics           accuracy_score, precision_score, recall_score, f1_score,
                  roc_auc_score, confusion_matrix, classification_report, roc_curve

## Motivation

I'm developing this project to conclude the Introduction to Data Science and Supervised Machine Learning, to enhance my personal portfolio with Data Science projects applied to real world data and last but not least, to study and contribute to the public discussion regarding public healthcare, public policies and the human development, the health role on that more specifically.
With projects of this nature, this one as an example, we can enhance and improve our discussions about our role as a society and how to better allocate resources and catch up higher development standards.

## the problem

 CMS publishes a Maternal Health file for every Medicare-certified US hospital. One
measure, PC_07a, is the risk adjusted rate of severe obstetric complications, and CMS labels each hospital Top, Middle or Bottom Performer on it. Roughly half the hospitals deliver too few babies to be scored and have no label.

## The question
Can we identify a likely Bottom Performer *without* using the complication
measure itself? If yes, it gives a way to build a watch list, including for hospitals CMS cannot score.

## Files

| File | Description |
| `maternal_health_analysis.ipynb` | The analysis notebook. Data understanding, cleaning, feature engineering, model comparison and evaluation, following CRISP-DM. Cell outputs are saved, so it renders on GitHub without running it. |
| `data/Maternal_Health-Hospital.csv` | Source data from CMS. 17,945 rows and 4,655 hospitals, with 4 measures reported per hospital. Reporting period 01/01/2025 to 12/31/2025. |
| `data/HOSPITAL_Data_Dictionary.pdf` | Official CMS data dictionary. The Maternal Health section documents the measures used here (PC_02, PC_07a, PC_07b and SM_7). |
| `images/` | Charts exported from the notebook, used in the blog post. |
| `README.md` | This file. |

## Results

I started this project trying to understand what a hospital with poor maternal outcomes looks like. What caught my attention was the map: among states with at least 25 scored hospitals, the share of Bottom Performers goes from about 8% in Kansas to 51% in New York. This makes state-level differences worth investigating, but it does not tell us why they happen. Patient populations, access to care and differences in reporting could all contribute.

Cesarean rates also showed a pattern. From the lowest to the highest cesarean-rate quintile, the share of Bottom Performers rises from 21.5% to 32.5%. That is an association, not evidence that cesareans caused the complications. The safety attestation, SM_7, was less informative in this comparison: 25.1% of hospitals reporting "Yes" were Bottom Performers, against 24.7% among other responses. Those other responses are not necessarily a confirmed "No", and this result does not mean safety programs have no value.

The model helped me see why accuracy is not enough. Predicting that no hospital is a Bottom Performer gets 74.9% accuracy, but finds none of them. Logistic regression gets 60.3% accuracy and finds about half, although only one in three flags is correct (ROC AUC 0.623). It gives some separation between groups, but I would not use it to judge a hospital or recommend care.

My next step would be to look for better information, not just a more complicated algorithm. This file does not describe staffing, detailed patient characteristics or the communities hospitals serve. I also explored the full sample before the split, and tested the model on only one split of labelled hospitals. New data and a stricter validation would be needed before using it as a watch list, especially for hospitals without a CMS label.

## Acknowledgements

- Data from the **Centers for Medicare & Medicaid Services (CMS)**, Hospital Downloadable
  Database, "Maternal Health - Hospital" dataset, obtained from the
  [CMS Provider Data Catalog](https://data.cms.gov/provider-data/). As a work of the
  U.S. federal government the data is in the public domain. CMS does not endorse this
  analysis, and any errors in interpretation are mine.
- Measure definitions (PC_02, PC_07a, PC_07b and SM_7) come from the official CMS data
  dictionary, included in this repository under `data/`.
- **Udacity**, for the project brief and for the CRISP-DM framing used to structure the
  analysis, as part of the Introduction to Data Science and Supervised Machine Learning
  course.


Blog post with the non-technical write-up:
[def]

[def]: https://s-campos-david.github.io/Intr_Data_Science_Course/