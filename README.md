# ***Data & AI Engineer – Categorization & Evaluation***
This summary presents key insights from the classification model's performance across business transaction categorization. Analysis is based on confusion matrices, classification reports, and misclassification patterns.

## ***Task 1:***

### **📊 Overall Metrics:**
Precision: 0.815

Recall: 0.817

F1 Score: 0.778

Interpretation: Strong general performance, though slightly uneven across imbalanced or sparse classes.

### **Classification Report Takeaways:**
Strong performers (F1 ≥ 0.95):

Cost of Goods Sold:Salon Supplies

Cash Clearing Account

Utilities

Weak performers (F1 = 0):

Gift Expense, Charitable Donation, Advertising and Promotion

Indicates room for label consolidation or improved sample support.

### **Confusion Matrix Observations**
High-volume categories like "Supplies and Materials" and "Cash Clearing Account" dominate predictions.

Misclassifications are common among semantically similar or vague classes, especially "Uncategorized Expense" and "Meals and Entertainment".

## ***Task 2:***

### **Key Misclassification Patterns:**
🔸 Uncategorized Expense (52/98 misclassified)
Over 53% of samples misclassified.

Frequent patterns: SEND E-TFR, Netflix, Temu, Apple.com.

Suggestion: Add fuzzy matching or custom rules to capture intent.

🔸 Meals and Entertainment (37/83 misclassified)
High confusion with lifestyle and food-related vendors.

Improve detection by tagging popular food vendors or using MCC code mapping.

🔸 Subcontractor (12/30 misclassified)
Errors due to generic e-transfer formats:
SEND E-TFR ***[name]

Recommend rule-based matching for frequent subcontractor names.

### **Top Misclassified Vendors:**
Common sources of label noise:

APPLE.COM/BILL

CHOICES YALETOWN

NANNYSERVICES.CA

AMZN Mktp CA

### ***Transaction Type & Channel Breakdown:***
Most misclassified entries are:

Type: place

Channel: in store or other

Indicates digital/online data provides clearer context for accurate classification.

### ***Unrepresented or Ignored Classes:***
Classes like Undeposited Funds, Research Expense, Gift Expense had:

0 examples, or

No correct predictions

Recommendation: Consider merging, re-labeling, or removing under-supported classes.

### ***Duplicate / Similar Label Conflicts***
Redundant or overly granular label names:

* "Owners Draw" vs. "30800 Owners Draw"

* "Computer and Software Expenses" vs. "61700 Computer and Software"

### ***Recommendations***

| Area	| Actionable Fix |
| ----- | -------------- |
| Label Granularity |	Merge semantically similar categories |
| Vague Vendors |	Apply rule-based overrides or fuzzy matching |
| Underrepresented Classes |	Remove or combine with related categories |
| E-Transfer Subcontractors |	Train model on specific known payees |
| In-store Vendor Ambiguity |	Add support via vendor lists or location tags |

## ***Task 3: Evaluation Metrics & Feedback Loop***

To ensure long-term performance and adaptability, I propose a lightweight evaluation pipeline that integrates bookkeeper feedback, monitors model quality, and supports semi-automated retraining.

### ***✅ Key Goals:***
1. Monitor Model Drift: Check if accuracy drops over time.

2. Catch Ambiguity: Flag predictions with low confidence.

3. Close Feedback Loop: Learn from bookkeeper corrections.

### ***📉 Metrics to Track in Production***
| **Metric** | **Purpose** |
| ------ | ------- |
| Rolling Accuracy / F1 |	Detect gradual performance degradation |
| Confidence Score Distribution |	Identify shifts in prediction certainty |
| Top-K Accuracy |	Evaluate if true label appears in top-K |
| Correction Rate |	% of predictions overridden by users |
| Category Confusion Trends |	Track specific misclassification patterns |

### ***Evaluation Pipeline WorkFlow:***

1. Prediction Phase:
    - For each transaction, return:
        - Predicted category
        - Confidence score
        - Top-K alternative categories (optional)
        - Flag if confidence < threshold (e.g., 0.6)

2. Human Review:
    - Low-confidence predictions shown to bookkeeper for manual tagging
    - Feedback logged to `corrections_log`

3. Evaluation:
    - Compare prediction vs. corrected label
    - Log confusion matrix and accuracy per class monthly
    - Alert on drift (e.g., >10% drop in accuracy or spike in corrections)

4. Retraining:
    - Periodically retrain on enriched dataset (original + corrected)
    - Use stratified sampling to handle class imbalance

### ***🧠 Confidence Score-Based Fallback Logic***
Leverage ConfidenceScore to route predictions smartly:

| Confidence Range | Action |
| ---------------- | ------ |
| > 0.85 | Auto-approve prediction |
| 0.6 – 0.85 | Suggest top 3 predictions for review |
| < 0.6	| Flag for mandatory manual classification |

**Bonus: Confidence scores can be logged per vendor to auto-learn which merchants require human input more frequently.**

