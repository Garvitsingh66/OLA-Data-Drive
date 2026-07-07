# OLA Data Drive — Ride Performance Hub & Cancellation Prediction

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat&logo=scikitlearn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-006400?style=flat)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=flat&logo=mysql&logoColor=white)

End-to-end analytics project on **50,000 OLA ride records** — combining a Power BI dashboard (business-facing KPIs and trends) with a machine learning model (predicting ride cancellations) built on the same dataset.

**Highlight:** This project includes a real data leakage investigation — an initial model scored a misleading ~93% accuracy, which was diagnosed, explained, and corrected to produce an honest result. See [Key Finding](#key-finding) below.

## Table of Contents
- [Project Overview](#project-overview)
- [Part 1: Power BI Dashboard](#part-1-power-bi-dashboard)
- [Part 2: ML Cancellation Prediction](#part-2-ml-cancellation-prediction)
- [Key Finding](#key-finding)
- [Tech Stack](#tech-stack)
- [Files](#files)
- [Future Work](#future-work)
- [Contact](#contact)

## Project Overview

This project has two connected parts:

1. **Power BI Dashboard** — a 4-page interactive dashboard analyzing bookings, revenue, ratings, and cancellations.
2. **ML Cancellation Prediction** — a classification model built on the same data, extending the BI findings with a predictive approach and a real investigation into data leakage.

---

## Part 1: Power BI Dashboard

A 4-page interactive dashboard built with Power BI, DAX, and Power Query on 50,000 ride records.

### Overview
![Overview](Images/Overview.png)
Summarizes overall ride volume, **66.97% booking success rate**, and **₹685 average booking value** across the full dataset.

### Bookings
![Bookings](Images/Bookings.png)
Breaks down ride volume trends across time, vehicle type, and payment method.

### Cancellations
![Cancellations](Images/Cancellations.png)
Investigates the **33% cancellation rate**, comparing driver-side vs. customer-side cancellation reasons using DAX measures. Statistical (scatter-based) analysis found **no strong correlation between wait time and cancellations** — a finding later confirmed independently by the ML model in Part 2.

### Ratings
![Ratings](Images/Ratings.png)
Tracks driver and customer rating trends across the dataset.

### Revenue
![Revenue](Images/Revenue.png)
Tracks booking value trends and revenue drivers across vehicle types and time periods.

---

## Part 2: ML Cancellation Prediction

**Goal:** Predict whether a ride will be cancelled, using only information genuinely available *before* the ride happens (booking-time features) — extending the BI dashboard's cancellation analysis into a predictive model.

### Approach

1. Loaded the same 50,000-record dataset used in the BI dashboard.
2. Defined a binary target: `is_cancelled` (from `Booking Status`).
3. **Investigated an initial model that scored ~93% accuracy** with Random Forest and XGBoost — found it was driven entirely by **data leakage**: features like Booking Value, Ride Distance, Driver Ratings, Customer Rating, and Payment Method are only recorded *after* a ride completes, so they were `NaN` for every cancelled ride by construction. The models were learning "missing value = cancelled," not any real cancellation driver.
4. Removed all leaky, post-outcome fields.
5. Engineered genuine pre-ride features instead:
   - `hour_of_day`, `day_of_week`, `is_weekend` (from booking timestamp)
   - Grouped `Pickup Location` / `Drop Location` into top-15 zones + "Other"
   - `Vehicle Type`
   - `cust_past_cancel_rate` — each customer's cancellation rate computed only from their *prior* rides (no leakage from the current ride's outcome)
6. Trained and compared Logistic Regression, Random Forest, and XGBoost on the corrected feature set.

### Results: Before vs. After Fixing Leakage

| Stage | Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|---|
| Before fix (leaky features) | Random Forest | 0.93 | 0.80 | 1.00 | 0.89 |
| Before fix (leaky features) | XGBoost | 0.93 | 0.80 | 1.00 | 0.89 |
| After fix (clean features) | Logistic Regression | 0.49 | 0.27 | 0.52 | 0.36 |
| After fix (clean features) | Random Forest | ~0.5 | ~0.3 | ~0.5 | ~0.3 |
| After fix (clean features) | XGBoost | ~0.5 | ~0.3 | ~0.5 | ~0.3 |

*Baseline (always predict "not cancelled"): 73.2% accuracy.*

The drop after removing leaky features is the point, not a failure — it shows the original 93% was an artifact of data leakage, not a real model.

### Key Finding

The corrected result shows that available pre-ride fields — time of day, day of week, vehicle type, pickup/drop zone, and customer cancellation history — carry **little independent predictive power** for cancellation, performing near or below the majority-class baseline.

This confirms and extends the BI dashboard's earlier finding that wait time doesn't correlate strongly with cancellations. Together, both analyses point to the same conclusion: **cancellations are likely driven by real-time operational factors not captured in this dataset** — e.g., live driver distance at time of booking, real-time traffic conditions, or surge pricing — rather than static, booking-time attributes.

### What This Demonstrates

- Diagnosing and correcting data leakage **twice** in the same pipeline, rather than reporting an inflated, misleading accuracy score
- Feature engineering under a realistic constraint (only using data available *before* the outcome)
- Correct model evaluation on imbalanced classes (precision/recall/F1/ROC-AUC — not just accuracy)
- Drawing a consistent conclusion across two different analytical approaches (BI + ML) on the same dataset
- Comfort reporting an honest negative result and explaining *why*, instead of overselling a number

---

## Tech Stack

| Category | Tools |
|---|---|
| BI & Visualization | Power BI, DAX, Power Query |
| Data Processing | Python (Pandas, NumPy), SQL |
| Machine Learning | Scikit-learn, XGBoost |
| Modeling Techniques | Logistic Regression, Random Forest, Gradient Boosting |

## Files

```
├── OLA_Dashboard.pbix                     # Power BI dashboard
├── ola_cancellation_prediction_v2.py      # Final ML pipeline (leakage-corrected)
├── Images/
│   ├── Overview.png
│   ├── Bookings.png
│   ├── Cancellations.png
│   ├── Ratings.png
│   └── Revenue.png
└── README.md
```

## Future Work

- Incorporate real-time features (live driver distance at booking, real-time traffic, surge pricing) if such data becomes available, to test whether they carry stronger predictive signal than static booking-time fields
- Try time-aware train/test splitting instead of random splitting, to better simulate real-world deployment
- Explore whether `cust_past_cancel_rate` has stronger signal when restricted to customers with a longer ride history

---

<details>
<summary><b>Resume bullet (click to expand)</b></summary>

> Built a ride-cancellation prediction pipeline on 50K records; diagnosed and corrected data leakage from post-outcome features (initial 93% accuracy → honest ~50%), engineering pre-ride features including customer cancellation history, and connected findings to an existing Power BI dashboard analysis.

</details>

---

## Contact

**Garvit Singh**
GitHub: [github.com/Garvitsingh66](https://github.com/Garvitsingh66)
LinkedIn: [linkedin.com/in/garvitsingh66](https://linkedin.com/in/garvitsingh66)

*Part of a broader data analytics portfolio — see [Airbnb Performance Dashboard](https://github.com/Garvitsingh66/AirBnb-Performance-Dashboard) and [Customer Behavior Dashboard](https://github.com/Garvitsingh66/Customer-Behavior-Dashboard) for related projects.*
