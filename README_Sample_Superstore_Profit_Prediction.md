# Sample Superstore Profit Prediction

**Project:** Sample Superstore Profit Prediction  
**Platform:** Altair AI Studio Educational 2026.0.2  
**Project seed:** `6729`  
**Target:** `Profit` (numeric regression)  
**Evaluation metric:** Absolute error / Mean Absolute Error (MAE)

## 1. Project overview

This project uses the Sample Superstore dataset to predict order-level Profit from selected order and product characteristics. The workflow covers data selection, feature engineering, leakage-aware data partitioning, model training, validation, model comparison, directional-error analysis, and a business-case recommendation.

The repository is intended to help a reviewer understand and reproduce the workflow without needing to know Altair AI Studio in advance. The native AI Studio process file and the dataset or its documented access path are the authoritative artifacts for exact reproduction.

## 2. Research question

Can a regression model predict Profit more accurately than a simple mean-prediction baseline, and which candidate offers the best balance of validation error and the project's stated business-cost assumptions?

## 3. Data and target

- **Dataset:** Sample Superstore, loaded from the AI Studio repository in the original workflow.
- **Target column:** `Profit`.
- **Predictors:** Selected product, order, discount, shipping, customer-segment, and geographic fields.
- **Excluded fields:** Identifier and name fields, along with fields not selected as predictors in the saved process.
- **Engineered attributes:** `Discount Band`, `Order Year`, `Order Month`, and `Order Quarter`.

### Data access

The original process retrieves Sample Superstore from the AI Studio repository. If the dataset is included with this repository, place the public dataset in the documented data folder and record its source and license here. If the data cannot be redistributed, document the public source and the steps required to import it into AI Studio instead. Do not commit private, restricted, or licensed data without permission.

## 4. Tools and workflow

### Software

- Altair AI Studio Educational 2026.0.2
- Sample Superstore dataset
- Microsoft Word or a compatible editor for the report and workflow documentation

### End-to-end workflow

1. **Retrieve data** — load Sample Superstore.
2. **Select attributes** — retain the chosen predictors and exclude unselected identifier/name fields.
3. **Set role** — assign `Profit` as the numeric label.
4. **Generate attributes** — create discount-band and order-date features.
5. **Partition data** — use seed `6729`; the first split is 70% training and 30% remaining data, then split the remaining portion 50/50 for validation and holdout. This yields approximately 70% training, 15% validation, and 15% holdout.
6. **Preprocess** — apply nominal-to-numerical encoding and missing-value replacement on the appropriate model branches.
7. **Train models** — fit the baseline, Linear Regression, Decision Tree, Random Forest, and Neural Network.
8. **Apply models** — generate predictions for the common validation partition.
9. **Evaluate** — compare validation absolute error/MAE.
10. **Analyze directional errors** — calculate underprediction and overprediction amounts and their averages.
11. **Compare business costs** — apply the explicitly stated Segment 7 planning assumptions.
12. **Select provisionally** — select the leading validation model, then evaluate the untouched holdout before any deployment decision.

### Leakage and partition discipline

The same validation partition and charter metric are used to compare the entrants. The holdout is reserved for final evaluation and should not be used to tune hyperparameters or select among models. Confirm the saved AI Studio process preserves this discipline before describing results as final.

## 5. Model tournament results

The recorded validation results are:

| Rank | Model                    | Validation MAE   | Standard deviation shown by AI Studio |
|---:  |---:                      |---:              |---:                                   |
| 1    | Decision Tree (depth 15) | 54.223           | 135.737                               |
| 2    | Neural Network           | 54.969           | 133.446                               |
| 3    | Random Forest            | 55.515           | 145.582                               |
| 4    | Naive Baseline           | 61.124           | 152.109                               |
| 5    | Linear Regression        | 95.303           | 214.290                               |

MAE is the average absolute difference between actual Profit and predicted Profit. Lower values indicate smaller average prediction errors. The `±` values are reproduced from the AI Studio PerformanceVector output; they should be described using the same interpretation as the course materials.

### Directional-error analysis

The validation predictions were also analyzed to separate underprediction from overprediction:

| Model                    | Average underprediction error | Average overprediction error |
|---                       |---:                           |---:                          |
| Decision Tree (depth 15) | 25.444                        | 28.779                       |
| Neural Network           | 30.742                        | 24.227                       |
| Random Forest            | 26.683                        | 28.832                       |
| Naive Baseline           | 29.170                        | 31.954                       |
| Linear Regression        | 56.177                        | 39.126                       |

Underprediction is the amount by which actual Profit exceeds predicted Profit. Overprediction is the amount by which predicted Profit exceeds actual Profit. The two average directional errors should approximately sum to MAE, allowing for displayed rounding.

## 6. Business-cost assumptions and selection

A separate Week 5 cost matrix was not available. Therefore, the following are **Segment 7 planning assumptions**, not historical company accounting figures or previously approved costs:

- Underprediction error price: `1.50` per error unit.
- Overprediction error price: `1.00` per error unit.
- Common operating cost: `2.00` per prediction.

Using the recorded directional-error averages, the expected cost calculation is:

`Expected cost = (average underprediction × 1.50) + (average overprediction × 1.00) + 2.00`

| Model                    | Expected cost per prediction under the stated assumptions |
|---                       |---:                                                       |
| Decision Tree (depth 15) | 68.945                                                    |
| Random Forest            | 70.857                                                    |
| Neural Network           | 72.340                                                    |
| Naive Baseline           | 77.709                                                    |
| Linear Regression        | 125.392                                                   |

**Current recommendation:** Decision Tree at depth 15 is the leading validation candidate under the recorded MAE and stated planning assumptions. This is a provisional model-selection conclusion, not proof of production readiness. Evaluate the untouched holdout once, after model selection, and replace the planning assumptions with defensible business estimates before operational use.

## 7. Reproducing the workflow

A reviewer unfamiliar with Altair AI Studio can follow these steps:

1. Install or open a compatible version of Altair AI Studio. The original project was built with Educational 2026.0.2.
2. Obtain the Sample Superstore dataset using the access instructions in the Data section.
3. Open the native process file included in this repository.
4. Check the Retrieve operator and confirm the dataset reference resolves.
5. Review the attribute-selection list, `Profit` label role, and generated attributes.
6. Verify the split seed is `6729` and the intended approximate partition is 70/15/15.
7. Inspect each model branch and confirm that all models are evaluated on the same validation partition with absolute error/MAE.
8. Run the process and compare the output to the recorded leaderboard. Small differences can occur if the dataset, software version, preprocessing, or operator parameters differ.
9. Keep the holdout untouched until the model has been selected.
10. Review the annotated workflow document for the model branches, error calculations, business assumptions, and limitations.


## 8. Responsible use, monitoring, and limitations

- **Provenance:** Record the dataset source, license/access restrictions, process version, software version, and seed.
- **Fairness checks:** Compare prediction errors across relevant groups such as Region, Segment, and Category. These checks are exploratory and should not be described as proof of fairness.
- **Feedback loops:** If predicted Profit changes which orders receive attention, the resulting decisions may change later observed outcomes and the data used for retraining.
- **Explanation:** Use the Decision Tree's structure and relevant feature patterns to explain predictions, while noting that explanations are not causal claims.
- **Accountability:** A business owner should review model-guided decisions and remain responsible for the final action.

### Monitoring plan

1. **Metric:** Track MAE and average underprediction/overprediction.
2. **Drift signal:** Review changes in Discount, Quantity, date features, Region, Segment, and Category, and monitor whether realized-profit errors increase.
3. **Trigger and cadence:** Review monthly; investigate if MAE rises roughly 10% above the established reference level or a meaningful data shift appears. Confirm the threshold with business stakeholders.
4. **Owner:** Assign the analytics/data-science team as the operational owner, with a named business stakeholder approving decisions and retraining.

### Limitations

1. The reported numbers are validation results from one partition and do not establish performance on future data.
2. The cost weights are illustrative planning assumptions and need business validation.
3. The model may perform differently under changing discounts, product mix, regions, or business conditions; the untouched holdout and ongoing monitoring are necessary before deployment.

## 9. AI assistance disclosure

AI assistance was used to help organize documentation, explain the workflow, and check the arithmetic in the model-comparison and business-cost tables. The reported model settings and results are based on the recorded Altair AI Studio outputs. The student remains responsible for verifying the repository artifacts, dataset access, and final submission.

## 10. Submission checklist

- [ ] Native Altair AI Studio process file opens and references the intended dataset.
- [ ] Dataset is included only if redistribution is permitted; otherwise the data access path is documented.
- [ ] Annotated workflow PDF and editable document are included.
- [ ] Plain-table leaderboard is included
- [ ] README filenames match the files actually present in the repository.
- [ ] Segment 7 planning assumptions are clearly labeled and not represented as Week 5-approved costs.
- [ ] Holdout has not been used for model tuning or selection.
