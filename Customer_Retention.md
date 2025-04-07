# Which Customers Are Worth Retaining?
_A Machine Learning and Optimization Framework for Targeted Customer Retention_

**Joshua Thompson**  
**Date: 03/28/2025**

---

## Table of Contents

1. [Business Context](#business-context)
2. [Exploratory Data Analysis](#exploratory-data-analysis)
    - [Summary Statistics](#summary-statistics)
3. [Customer Churn Analysis](#customer-churn-analysis)
    - [Machine Learning Model for Churn Prediction](#machine-learning-model-for-churn-prediction)
    - [Model Performance Evaluation](#model-performance-evaluation)
    - [Threshold and Trade-Off Analysis](#threshold-and-trade-off-analysis)
4. [Customer Segmentation](#customer-segmentation)
    - [Determining Optimal Number of Clusters](#determining-optimal-number-of-clusters)
    - [Customer Segmentation Visualization](#customer-segmentation-visualization)
    - [Customer Segments & Personas](#customer-segments--personas)
5. [Customer Retention](#customer-retention)
    - [Baseline Heuristic Approach](#baseline-heuristic-approach)
    - [Knapsack Optimization Strategy](#knapsack-optimization-strategy)
    - [Comparative Results](#comparative-results)
6. [Recommendations & Future Work](#recommendations--future-work)

---

## 1. Business Context

The *Telco-Customer-Churn* dataset contains detailed records for over 7,000 customers of a telecommunications company. Each row represents a single customer, capturing both demographic and service-related attributes. Key fields include:

- **Demographics:** Basic information such as gender, senior citizen status, partner, and dependents.
- **Subscription Details:** Contract type (month-to-month, one-year, two-year), payment method, and paperless billing preferences.
- **Service Usage:** Internet service type (DSL, Fiber optic, None), phone and multiple line usage, and optional add-on services (e.g., online security, online backup, device protection, streaming TV, streaming movies).
- **Financials:** *MonthlyCharges* (amount paid per month) and *TotalCharges* (cumulative amount paid).
- **Churn Indicator:** A binary flag showing whether the customer has discontinued service.

In the telecommunications industry, customer churn poses a major challenge, particularly for subscription-based models. Reducing churn improves customer lifetime value (CLV), increases marketing efficiency, and reinforces brand loyalty. Even small improvements in churn rates can lead to significant financial gains.

**Business Objectives:**

1. Predict which customers are most likely to churn.
2. Segment customer groups based on behavior and churn risk.
3. Prioritize customers for retention under budget constraints.
4. Maximize the expected net profit from retained customers.

This analysis combines machine learning and budget-constrained optimization to not only predict churn but also determine which customers to retain in order to maximize profitability.

---

## 2. Exploratory Data Analysis

### Summary Statistics
<img src = "images/Churn_Project/Picture2.png?raw=true"/>
- Approximately **26.5%** of customers have churned, while **73.5%** remain loyal.
- **Customer Tenure:** Reveals two main groups—short-term churners and long-term loyal customers.
- **Monthly Charges:** Right-skewed distribution, with most customers paying between $20–$50, though a notable portion pays higher amounts.
- **Total Charges:** Highly skewed due to the interplay between tenure and monthly charges, with higher values seen in long-tenured, high-value customers.

These insights emphasize the need for targeted retention strategies to address the high churn rate.

---

## 3. Customer Churn Analysis

### Machine Learning Model for Churn Prediction

**Model Comparison:**

| Model                | Accuracy | Recall | Precision | F1-Score | AUC   |
|----------------------|----------|--------|-----------|----------|-------|
| Logistic Regression  | 0.739    | 0.805  | 0.505     | 0.620    | 0.843 |
| Naïve Bayes          | 0.687    | 0.857  | 0.453     | 0.592    | 0.819 |
| CART                 | 0.686    | 0.836  | 0.458     | 0.587    | 0.797 |

Logistic Regression achieved the best overall performance, balancing recall, precision, and AUC. Although Naïve Bayes excelled in recall, its precision was lower, and CART—despite its interpretability—underperformed in both areas.

> *This section includes a plot that visualizes the average effect of each feature on churn probability as learned by the logistic regression model.*

**Key Insights:**

- **Long-Term Contracts Reduce Churn:** Customers with 1-year and especially 2-year contracts are significantly less likely to churn.
- **Fiber Optic Internet Increases Churn:** Customers using fiber optic internet show a higher risk.
- **Engagement with Support Services Lowers Churn:** Usage of support services (technical support, online security, phone services) correlates with lower churn.
- **Streaming Services & Paperless Billing Increase Churn Slightly:** These factors may indicate a greater likelihood to switch providers.
- **High CLV Segments Are More Loyal:** Customers with medium to high lifetime value are less prone to churn.

### Model Performance Evaluation

The confusion matrix from the Logistic Regression model highlights:

- **True Positives:** 1,504 churned customers correctly identified.
- **True Negatives:** 3,701 non-churners correctly identified.
- **False Positives:** 1,473 non-churners misclassified as churners.
- **False Negatives:** 365 churned customers that were missed.

**Implications:**

- A significant number of false positives suggest that some resources might be used for customers unlikely to churn.
- Low false negatives indicate that most actual churners are successfully identified.

### Threshold and Trade-Off Analysis

Adjusting the classification threshold impacts performance:

- **F1-Score vs. Threshold:** Lowering the threshold captures more at-risk customers.
- **Precision vs. Recall:** A higher recall ensures most churners are detected, while maintaining an acceptable false positive rate.

An optimal threshold of **0.25** was selected, balancing high recall (approximately 81% of churners identified) with acceptable precision, ensuring effective targeting of retention efforts.

---

## 4. Customer Segmentation

Segmenting customers helps tailor retention strategies.

### Determining Optimal Number of Clusters

Using the Elbow Method, a sharp bend at **K = 3** indicated that three clusters provide a good balance between compression and complexity.

### Customer Segmentation Visualization (PCA)

K-means clustering, combined with Principal Component Analysis (PCA) for dimensionality reduction, was used to visualize the customer segments. Each point represents a customer, colored by cluster assignment.

### Customer Segments & Personas

**Cluster Summaries:**

- **Cluster 1 (High Risk – 85% likelihood of churn):**
  - Mostly month-to-month contracts (94%).
  - Low tenure and low autopay usage.
  - Heavy use of fiber internet; limited support services.
  
- **Cluster 2 (Loyal – 0% likelihood of churn):**
  - Long-tenured customers.
  - Low monthly charges and strong service adoption.
  - High percentage with phone service.
  
- **Cluster 3 (Moderate Risk – 13% likelihood of churn):**
  - Highest revenue customers.
  - Long tenure and high overall service use.

---

## 5. Customer Retention

Two approaches were evaluated for targeting retention efforts:

### Baseline Heuristic Approach

Targets all customers whose expected benefit exceeds the retention cost, without budget constraints.

**Metrics:**

- **Total Customers:** 7,032
- **Identified for Retention:** 2,620
- **Total Expected Benefit:** $887,798
- **Retention Cost:** $632,057
- **Total Expected Profit:** $255,741

### Knapsack Optimization Strategy

Solves a binary knapsack problem to maximize expected net profit while adhering to a fixed retention budget.

#### Scenario 1: Full Optimization (No Segmentation)

- **Retention Budget:** $250,000
- **Identified for Retention:** 775 customers
- **Total Expected Benefit:** $387,769
- **Retention Cost:** $250,000
- **Total Expected Profit:** $137,769

#### Scenario 2: Cluster-Based Budget Allocation

- **Retention Budget:** $250,000
- **Targets:**
  - **Cluster 1:** 487 customers
  - **Cluster 3:** 106 customers
- **Total Expected Benefit:** $372,468
- **Retention Cost:** $250,000
- **Total Expected Profit:** $122,468

Although Scenario 2 sacrifices about 11% of potential net profit compared to full optimization, it supports strategic objectives like maintaining market share among moderate-risk customers.

### Comparative Results

The retention funnel:

- **Start:** 7,032 total customers.
- **Churn Model:** Identifies 2,620 likely churners.
- **Knapsack Optimization (Scenario 2):** Selects 593 customers as optimal retention targets.

This strategy ensures a balance between maximizing net profit and preserving market share within budget constraints.

---

## 6. Recommendations & Future Work

**Key Takeaways:**

- The Logistic Regression model delivers actionable churn probabilities with high recall.
- Customer segmentation reveals distinct behavioral patterns for targeted retention.
- A rule-based approach may lead to inefficient resource allocation under budget constraints.
- The knapsack optimization framework increases profit per dollar by focusing on high-value, high-risk customers.

**Recommendations:**

- **Adopt a data-driven retention strategy:** Combine machine learning churn prediction with optimization-based retention planning.
- **Focus on high-risk, high-value customers:** Allocate retention resources where they yield the highest net benefit.
- **Maintain flexibility:** Adapt retention strategies to dynamic budgets and changing business conditions.

**Future Considerations:**

1. **Personalized Retention Offers:** Tailor incentives based on customer cluster characteristics or individual churn probabilities.
2. **Model Monitoring and Updates:** Regularly update churn models and optimization parameters to reflect evolving customer behavior.
3. **Integrate CLV Segmentation:** Incorporate predicted Customer Lifetime Value (CLV) into the optimization process for better prioritization.

By implementing this approach, the business can enhance customer lifetime value, reduce churn-related revenue loss, and maintain long-term profitability.

---

*End of Report*
