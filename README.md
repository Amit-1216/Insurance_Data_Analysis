# Health Insurance Claims: What Is Associated with Higher Claims?

**A question-driven analysis of 1,340 policyholders**

Insurers need to understand which characteristics are associated with larger claims. This project analyzes 1,340 policyholders described by age, gender, BMI, blood pressure, diabetic status, number of children, smoking status, and region, together with the amount they claimed.

The work is organized around one central puzzle: several attributes look related to claims in a simple comparison, but many of those relationships turn out to be **composition effects** — they appear only because the attribute is correlated with smoking. The goal is to separate what genuinely matters from what only appears to.

## Business Questions

1. How large is the claim gap between smokers and non-smokers, and how much of total claims do smokers account for?
2. Does gender really affect claims, or is it a side-effect of who smokes?
3. How do blood pressure, BMI, and diabetes relate to smoking, gender, and region?
4. Do regional claim differences persist after accounting for smoking?
5. Does age matter?
6. Does the effect of BMI on claims depend on smoking status?
7. Is blood pressure related to claims independently of smoking?
8. Do diabetes and the number of children matter?
9. Which combination of factors explains claims best?
10. Which segments of policyholders account for the largest claims?

## Key Insights

1. **Smoking is the dominant factor.** Smokers claim 3.8x more on average, are 20% of policyholders but 49% of claim dollars, and make up 98% of the top-10% claims. Smoking alone explains 62% of claim variance.
2. **BMI matters only for smokers.** Each BMI point adds about $1,470 for smokers and about $80 for non-smokers; obese smokers average $41,558 vs. $21,363 for smokers below BMI 30.
3. **Blood pressure carries independent information** (ρ = 0.22 within non-smokers, 0.41 within smokers), although smokers have markedly higher readings (30% at 110+ vs. 2%).
4. **Gender's raw effect is a smoking composition effect.** The male average is higher because men smoke more (23% vs. 17%); medians are nearly identical, and among non-smokers women actually claim slightly more.
5. **The northeast is more expensive, and only partly because of smoking.** Its smoker share is highest, but non-smokers there also claim about 40–57% more than in other regions.
6. **Age and diabetes show no association with claims**, and number of children only a small one, for non-smokers only (about $700 per child).
7. **A short list of variables is enough.** The final model explains 77% of variation (adjusted R²), mostly through smoking, the smoking × BMI interaction, and blood pressure.
8. **The data is probably synthetic or unusual** (no age effect, 48% diabetic, no BMI–diabetes link, blood pressure floored at 80), so results should not be generalized to a real population.

## Conclusion

Claims in this dataset are organized around **smoking status, with BMI and blood pressure acting mainly through it**. A small group of smokers with high BMI (about 11% of policyholders) accounts for about a third of claim dollars, while age, gender, and diabetes contribute essentially nothing once smoking is accounted for. The analytical value of the project lies in separating apparent effects (gender, part of the regional gap, part of blood pressure's correlation) from real ones (smoking, the smoking–BMI interaction, and independent blood-pressure and regional effects).

### Limitations

- Observational data: all relationships are associations. Blood pressure and BMI may be consequences of smoking, and no time dimension exists.
- The dataset appears synthetic or selected (see Data Quality, and Q6/Q9 in the notebook), so findings should not be applied to a real insured population without validation.
- Blood-pressure units are undocumented; the 10-point bands used are descriptive only.
- The linear model does not capture the apparent threshold near BMI 30, and residual structure remains; some groups (e.g. underweight smokers, n=5) are very small.
- The original data source was not reachable at execution time; a public copy of the same file was used (the loader tries the original first).

### Possible Next Steps

- Fit a tree-based model to test for thresholds and interactions.
- Evaluate prediction accuracy on hold-out data.
- Model the log of the claim amount to handle skewness.

## Data

- **Source:** insurance claims data (1,340 rows, 11 columns): `PatientID`, `age`, `gender`, `bmi`, `bloodpressure`, `diabetic`, `children`, `smoker`, `region`, and `claim`.
- **Loading:** the notebook loads the data from a published Google Sheet URL, with a public-copy fallback if the original source isn't reachable — no local file is required to run it as-is.

## Data Quality Issues Found and Corrected

| Issue | Action |
|---|---|
| `PatientID` has a 0.9999 Spearman correlation with `claim` (the file is sorted by claim amount) | Dropped — it would leak the outcome into any model |
| 8 missing values (5 `age`, 3 `region`), concentrated at the very bottom of the claim range | Not imputed; each analysis uses the rows that have the columns it needs |

## Methods

- Interpretable groupings: `bmi_category` (WHO cut-offs), `age_group`, `bp_band`, `children_group`.
- Group comparisons with Mann–Whitney / Kruskal–Wallis tests, and chi-square tests for categorical associations.
- Stratified analysis (e.g. blood pressure by gender *within* smoking status) to distinguish real effects from composition effects.
- Sequential OLS regression (via `statsmodels`), adding predictors one at a time and tracking adjusted R², including a smoker × BMI interaction term.
- Segment analysis combining smoking, BMI, and blood pressure into risk tiers ranked by average claim.

## Tools

- Python
- Pandas
- NumPy
- SciPy (`scipy.stats`)
- statsmodels
- Matplotlib
- Seaborn

## Project Structure

```text
insurance-claims-analysis/
├── notebooks/
│   └── Insurance_Data_Analysis.ipynb
└── README.md
```

No `datasets/` folder is needed — the notebook loads its data directly from a hosted Google Sheet.

## How to Run

```bash
git clone <your-repository-url>
cd insurance-claims-analysis
pip install pandas numpy scipy statsmodels matplotlib seaborn jupyter
jupyter notebook
```

Open `notebooks/Insurance_Data_Analysis.ipynb` and run the cells sequentially. An internet connection is needed the first time, to fetch the data from its hosted source.
