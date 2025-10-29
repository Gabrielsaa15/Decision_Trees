# Assignment 4 - Decision Trees
## Points 1.1 and 1.2 - Heart Disease Classification

## 1.1 Data Cleaning

### Process Summary

**1. Variable Renaming**
- Renamed 14 columns to: `age`, `sex`, `cp`, `restbp`, `chol`, `fbs`, `restecg`, `thalach`, `exang`, `oldpeak`, `slope`, `ca`, `thal`, `hd`

**2. Missing Values**
- Original dataset: 303 rows
- After removing missing values: 297 rows
- Only 6 rows removed (2% data loss)

**3. Categorical Variables to Dummies**
- Converted 8 categorical variables: `sex`, `cp`, `fbs`, `restecg`, `exang`, `slope`, `ca`, `thal`
- Applied one-hot encoding with `drop_first=True`
- Final shape: 297 rows × 21 columns

**4. Binary Target Variable**
- Created `y`: 1 if patient has heart disease (`hd > 0`), 0 otherwise
- Distribution: 160 without disease (53.9%), 137 with disease (46.1%)
- Dataset is relatively balanced

---

## 1.2 Data Analysis

### 1. Train-Test Split and Initial Tree

- Split: 70% training (207 samples), 30% test (90 samples)
- Random state: 123

**Initial Tree Results:**
- Depth: 10 levels
- Leaves: 35
- Test Accuracy: 73.33%

The high number of leaves suggests potential overfitting.

### 2. Confusion Matrix - Initial Tree

|                    | Predicted: No HD | Predicted: Has HD |
|--------------------|------------------|-------------------|
| **Actual: No HD**  | 35               | 14                |
| **Actual: Has HD** | 10               | 31                |

**Interpretation:**

- **True Negatives (35):** Correctly identified 71.4% of healthy patients
- **False Positives (14):** 28.6% of healthy patients incorrectly flagged with disease. These patients might undergo unnecessary tests.
- **False Negatives (10):** 24.4% of disease cases missed. This is concerning in medical context as these patients might not receive necessary treatment.
- **True Positives (31):** Correctly detected 75.6% of disease cases
- **Overall Accuracy:** 73.33%

The model shows reasonable sensitivity (75.6%) but the false negative rate is concerning for clinical applications.

### 3. Cross-Validation for Pruning

**Method:**
- Generated 50 alpha values logarithmically spaced between e⁻¹⁰ and 0.05
- 4-fold cross-validation with random_state = 123

**Results:**
- Optimal alpha: 0.009747
- Best CV accuracy: 75.44%

The cross-validation accuracy being higher than the unpruned tree's test accuracy confirms overfitting in the initial model.

### 4. Inaccuracy Rate vs Alpha

The plot shows a characteristic U-shaped curve:

- **Low alpha (< 0.001):** Minimal pruning, tree remains complex with low but stable inaccuracy
- **Optimal region (α ≈ 0.01):** Inaccuracy minimized at α = 0.009747
- **High alpha (> 0.02):** Excessive pruning causes inaccuracy to increase sharply

This demonstrates the bias-variance tradeoff: too little pruning causes overfitting (high variance), while too much pruning causes underfitting (high bias).

### 5. Pruned Tree Results

**Pruned Tree Characteristics:**
- Depth: 8 levels (reduced from 10)
- Leaves: 19 (reduced from 35, a 46% decrease)
- Test Accuracy: 73.33% (maintained)

**Confusion Matrix - Pruned Tree:**

|                    | Predicted: No HD | Predicted: Has HD |
|--------------------|------------------|-------------------|
| **Actual: No HD**  | 36               | 13                |
| **Actual: Has HD** | 11               | 30                |

**Comparison with Initial Tree:**

| Metric | Initial | Pruned | Change |
|--------|---------|--------|--------|
| True Negatives | 35 | 36 | +1 |
| False Positives | 14 | 13 | -1 |
| False Negatives | 10 | 11 | +1 |
| True Positives | 31 | 30 | -1 |
| Accuracy | 73.33% | 73.33% | 0% |
| Leaves | 35 | 19 | -46% |

**Interpretation:**

The pruned tree achieves the same accuracy with significantly less complexity. While we gained one false negative (slightly worse for patient safety), we reduced false positives and dramatically improved model simplicity. The pruned model will generalize better to new patients.

From a medical perspective, the increase of one false negative is a trade-off, but the simpler tree is more interpretable for clinicians and more reliable for future predictions. The 46% reduction in complexity without accuracy loss demonstrates successful pruning.

---

## Summary

**Key Findings:**
- Successfully processed 297 patient records with minimal data loss
- Initial tree showed overfitting with 35 leaves
- Cross-validation identified optimal alpha = 0.009747
- Pruned tree reduced complexity by 46% while maintaining 73.33% accuracy
- Model shows reasonable performance for a screening tool but false negative rate (24-27%) requires careful clinical interpretation

**Recommendations:**
The pruned decision tree is suitable as a preliminary screening tool but should not replace comprehensive medical evaluation. Patients flagged as high-risk should undergo additional diagnostic procedures.
