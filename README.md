# Green Hydrogen Policy Scoring and Cost Projections Towards 2050

Authors: İlker Mert, Hüseyin Yağlı  
Corresponding author: ilkermert@osmaniye.edu.tr

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains the supplementary data and methodological documentation for the research article:

"Artificial Intelligence Based Policy Scoring and Probabilistic Cost Projections for Green Hydrogen Towards 2050"

## 📋 About the Study

This study introduces a novel framework that combines Natural Language Processing (NLP) with techno-economic modeling to project green hydrogen costs. The methodology consists of two main components:

1. AI-Driven Policy Analysis: Policy documents from 32 countries were analyzed using zero-shot classification to extract SWOT and PESTEL categories, which were converted into quantitative policy strength scores.

2. Probabilistic Cost Projections: These policy scores were integrated into a dynamic learning curve model within a Monte Carlo simulation framework to project Levelized Cost of Hydrogen (LCOH) from 2023 to 2050 under four scenarios.

## 🔬 Methodological Overview

### Policy Analysis Pipeline

The policy text analysis uses zero-shot classification with the XLM-RoBERTa large XNLI model [53,54]:

```python
# Zero-shot classification setup
classifier = pipeline("zero-shot-classification", 
                      model="joeddav/xlm-roberta-large-xnli")
swot_labels = ["Strength", "Weakness", "Opportunity", "Threat"]
pestel_labels = ["Political", "Economic", "Social", 
                 "Technological", "Environmental", "Legal"]

# Classify each sentence
swot_preds = classifier(sentences, swot_labels, multi_label=False)
pestel_preds = classifier(sentences, pestel_labels, multi_label=False)
```

The policy strength score is calculated as (Equation 1 in the manuscript):

```python
# Policy score calculation
raw_score = (N_political + 0.5N_Strength - 0.5N_Threat) / N_total
# Normalized to [0,1] range (Equation 2)
policy_score = (raw_score - min_score) / (max_score - min_score)
```

### LCOH Projection Model

The learning curve incorporates policy scores to adjust capacity doubling time (Equation 11):

```python
# Adjust doubling time based on policy strength
adjusted_doubling = max(2.0, base_doubling  (1 - 0.4  policy_score))

# Annual learning factor (Equation 8)
ann_learn_factor = (1.0 - learning_rate)(1.0/adjusted_doubling)

# Monte Carlo simulation (1000 iterations per country-scenario)
for iteration in range(N_MC):
    capex_val = initial_capex
    other_val = initial_other_costs
    for year in years[1:]:
        capex_val = capex_val  ann_learn_factor
        other_val = other_val  ann_other_factor
        lcoh_vals.append(capex_val + other_val)
```

## 📁 Repository Contents

| Content | Description |
|---------|-------------|
| Document Metadata | Complete metadata for all policy documents analyzed (Table S12) |
| Classification Results | Sentence-level SWOT and PESTEL classification outcomes for all documents |
| LCOH Projections | Monte Carlo simulation results for all countries and scenarios |
| Policy Scores | Calculated policy strength scores for each country |
| Sensitivity Analysis | Robustness check results for key parameters |
| Baseline Cost Data | Baseline LCOH and electrolyser cost data |

> Note on Policy Texts: The full text of policy documents cannot be shared due to copyright restrictions. However, Table S12 provides complete metadata for all documents, and all sentence-level classification results are included.

## 📊 Model Validation

The zero-shot classification model was validated against manual annotation of 50 randomly selected sentences:

```python
# Validation metrics
from sklearn.metrics import f1_score, precision_score, recall_score

print(f"SWOT F1-Score (weighted): {f1_score(y_true_swot, y_pred_swot, average='weighted'):.3f}")
print(f"PESTEL F1-Score (weighted): {f1_score(y_true_pestel, y_pred_pestel, average='weighted'):.3f}")
```

| Metric | SWOT | PESTEL |
|--------|------|--------|
| Precision (weighted) | 0.82 | 0.87 |
| Recall (weighted) | 0.82 | 0.84 |
| F1-Score (weighted) | 0.82 | 0.84 |

Inter-rater agreement (Cohen's Kappa): 0.59 (SWOT), 0.67 (PESTEL)

## 📊 2050 LCOH Projections (USD/kgH₂)

The table below presents the 2050 LCOH projections for all 32 countries under four scenarios, corresponding to Figure 7 in the manuscript. Baseline 2023 values are also shown for reference.

| Countries | LCOH 2023 | Failed Transition | Market Driven | Paris Pathway | Policy Driven |
|-----------|-----------|-------------------|---------------|---------------|---------------|
| Austria | 8.87 | 7.50 | 5.34 | 3.86 | 6.60 |
| Belgium | 8.28 | 7.00 | 4.93 | 3.48 | 6.11 |
| Bulgaria | 8.16 | 6.87 | 4.82 | 3.36 | 6.05 |
| China | 3.58 | 6.15 | 2.56 | 0.71 | 3.26 |
| Croatia | 8.95 | 7.60 | 5.42 | 3.95 | 6.64 |
| Czechia | 8.43 | 7.09 | 5.04 | 3.60 | 6.22 |
| Denmark | 6.82 | 5.67 | 3.79 | 2.51 | 4.92 |
| Estonia | 7.90 | 6.63 | 4.63 | 3.19 | 5.79 |
| Finland | 4.47 | 6.55 | 4.54 | 3.17 | 5.76 |
| France | 7.65 | 6.41 | 4.45 | 3.07 | 5.62 |
| Germany | 10.15 | 8.61 | 6.31 | 4.75 | 7.59 |
| Greece | 8.18 | 6.90 | 4.81 | 3.39 | 6.06 |
| Hungary | 11.01 | 9.38 | 6.99 | 5.28 | 8.32 |
| India | 4.62 | 6.63 | 3.42 | 2.02 | 4.42 |
| Ireland | 9.42 | 7.97 | 5.78 | 4.23 | 7.10 |
| Italy | 11.11 | 9.41 | 7.06 | 5.34 | 8.39 |
| Latvia | 6.85 | 5.74 | 3.83 | 2.50 | 4.95 |
| Lithuania | 6.86 | 5.74 | 3.84 | 2.52 | 4.96 |
| Luxembourg | 7.78 | 6.53 | 4.49 | 3.15 | 5.76 |
| Malta | 8.99 | 7.57 | 5.47 | 3.94 | 6.71 |
| Netherlands | 8.98 | 7.60 | 5.43 | 3.93 | 6.70 |
| Norway | 5.61 | 4.70 | 2.93 | 1.75 | 4.04 |
| Poland | 13.59 | 11.56 | 8.91 | 6.99 | 10.45 |
| Portugal | 5.18 | 6.88 | 4.85 | 3.41 | 6.04 |
| Romania | 9.17 | 7.76 | 5.57 | 4.09 | 6.86 |
| Slovakia | 10.86 | 9.21 | 6.88 | 5.16 | 8.21 |
| Slovenia | 8.23 | 6.87 | 4.86 | 3.46 | 6.07 |
| Spain | 7.15 | 5.99 | 4.06 | 2.73 | 5.20 |
| Sweden | 4.87 | 6.75 | 4.70 | 3.31 | 5.92 |
| Switzerland | 6.88 | 5.73 | 3.85 | 2.54 | 5.00 |
| United Kingdom | 10.67 | 9.08 | 6.67 | 5.10 | 8.09 |
| United States | 5.50 | 7.05 | 3.14 | 1.37 | 4.09 |

### Selected Findings

- Lowest projected 2050 LCOH (Paris Pathway): China (0.71 USD/kg), United States (1.37 USD/kg), Norway (1.75 USD/kg)
- Highest projected 2050 LCOH (Paris Pathway): Poland (6.99 USD/kg), Italy (5.34 USD/kg), Hungary (5.28 USD/kg)
- Policy contribution: Up to 17.6% additional cost reduction in high-policy-strength countries (China, under Paris Pathway)

```python
# Example: Isolating policy effect for China under Paris Pathway
policy_contribution = lcoh_with_policy - lcoh_without_policy
print(f"Policy contribution: {policy_contribution:.2f} USD/kg ({policy_contribution/lcoh_without_policy100:.1f}%)")
```

## 📌 Data Sources

### Electrolyser Costs
- Baseline CAPEX value: European Hydrogen Observatory (EHO), 2024 [69] - Median value: 686 USD/kW
- Learning curve methodology: IRENA (2020) [6] and Schoots et al. (2008) [67]
- Global cumulative capacity: IEA Global Hydrogen Review (2023, 2024) [43,44] - Initial capacity: 1.4 GW (2023)
- Operation & Maintenance costs: 
  - Fixed O&M: 2% of CAPEX (IRENA, 2020) [6]
  - Variable O&M: 0.1-0.3 USD/kg (Frieden & Leker, 2024) [8]

### Baseline LCOH Data
- Source: European Hydrogen Observatory (EHO), 2024 [69]
- Coverage: Grid electrolysis technology costs including CAPEX, grid charges, OPEX, taxes, and wholesale electricity costs for 32 countries

### Policy Documents
- Document corpus: Publicly available national hydrogen strategies and energy policies from 32 countries (see Table S12 for complete metadata)
- Key sources include: Hydrogen Council reports [31,40], IEA Global Hydrogen Review series [43,44], U.S. National Clean Hydrogen Strategy [32], U.K. Hydrogen Strategy [33], UN Energy reports [34,35], World Bank analyses [42], WTO [39], GH2 [37], Hydrogen Europe [45], REN21 [36,46], and APERC [48]

### Scenario and Technical Parameters
- Scenario framework: Based on IEA scenarios [59,60,61,62]
- WACC: 8% (constant, based on IRENA 2024) [5]
- Plant lifetime: 20 years [60]
- Capacity factor: 70% (literature assumption)
- Electrolyser efficiency: 50 kWh/kg [6]

## 📚 Model References

The zero-shot classification in this study uses the following models:

[53] Conneau, A., Khandelwal, U., Goyal, N., Chaudhary, V., Wenzek, G., Guzmán, F., Grave, E., Ott, M., Zettlemoyer, L., & Stoyanov, V. (2020). Unsupervised cross-lingual representation learning at scale. Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics, 8440–8451. https://doi.org/10.18653/v1/2020.acl-main.747

[54] Davison, J. (2020). joeddav/xlm-roberta-large-xnli. Hugging Face. https://huggingface.co/joeddav/xlm-roberta-large-xnli

## 📄 Citation

If you use this data or methodology in your research, please cite:

```
Mert, İ., & Yağlı, H. (2024). Artificial Intelligence Based Policy Scoring 
and Probabilistic Cost Projections for Green Hydrogen Towards 2050. 
[Journal Name], [Volume](Issue), [Pages].
```

## ⚖️ License

This supplementary data is made available under the MIT License.

## ✉️ Contact and Code Availability

The complete code used in this study is accessible from the corresponding author upon reasonable request.

For questions regarding the data or methodology, please contact:
İlker Mert - ilkermert@osmaniye.edu.tr

---

This README accompanies the supplementary materials for the article referenced above. Last updated: March 2026

---

