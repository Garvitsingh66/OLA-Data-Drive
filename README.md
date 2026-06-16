# Ola Data Drive — Ride Performance Hub

An end-to-end data analytics project analyzing **50K ride-booking records** from Ola. This project utilizes a robust modern data stack—combining **SQL** for data warehousing/cleaning, **Python** for preliminary data exploration, and **Power BI (DAX)** for interactive diagnostic and predictive dashboard architecture.

The dashboard provides actionable business insights into ride fulfillment dynamics, revenue leakage via cancellations, customer/driver sentiment loops, and vehicle performance optimization.

---

## 🛠️ Tech Stack & Architecture
* **Data Source:** Kaggle (Ola Ride Dataset)
* **Database:** SQL (Data Cleaning, Schema Validation, Null Handling, and KPI View Creations)
* **Scripting:** Python (Exploratory Data Analysis, Data Profiling)
* **BI Platform:** Microsoft Power BI
* **Analytical Engine:** DAX (Data Analysis Expressions) for complex dynamic measures, time intelligence, and operational tracking.

---

## 📊 Core KPI Matrix (Executive Summary)
* **Total Bookings:** 50,000
* **Gross Revenue:** ₹34.27M
* **Average Booking Value:** ₹685.35
* **Average Ride Distance:** 17.04 km
* **Overall Success Rate:** 66.97%
* **Total Cancellation Rate:** 33.03%

---

## 🖥️ Dashboard Views & Analytical Breakdown

### 1. Overview Dashboard
*Comprehensive high-level view monitoring health metrics across the entire platform.*

![Overview](./Overview.png)

* **Visual Elements:** KPI Cards, Booking Status Donut Chart, Vehicle Type Bar Chart, and Hourly Booking Heat Matrix.
* **Key Insights:** * Out of 50K total bookings, **33.48K (66.97%)** were completed successfully.
  * Operational leakage splits between **Driver Cancellations (19.22%)** and **Customer Cancellations (7.6%)**.
  * Demand is uniformly distributed across ride classes (Prime Plus, Bike, Prime Sedan, SUV, Auto, eBike), with each vehicle type hovering near the **7K booking mark**.

### 2. Bookings Analysis
*Granular tracking of volume distributions across timelines and geography.*

![Bookings](./Bookings.png)

* **Visual Elements:** Daily Booking Volume Trendline, Payment Method Distribution, and Top 10 Pickup Locations.
* **Key Insights:**
  * Daily volume remains highly consistent, fluctuating reliably between **1,500 to 1,750 bookings per day** across a 31-day cycle.
  * **Payment Split:** Cash and Wallet capture the majority share of transactions (combining for over **49.5%** of payment methods), while digital UPI tracks closely at **16.86%**.
  * **Hotspots:** `Area-8`, `Area-9`, and `Area-48` emerge as the top 3 highest-density pickup demand centers.

### 3. Cancellations & VTAT Analysis
*Deep dive into service friction, drop-off reasons, and Vehicle Turnaround Time (VTAT).*

![Cancellations](./Cancellations.png)

* **Visual Elements:** Driver Reason Breakdown, Customer Reason Breakdown, Cancellation Rate by Vehicle Type, and VTAT vs. Cancellation Rate Scatter Plot.
* **Key Insights:**
  * The single largest driver-side bottleneck is tracked under unrecorded reasons, alongside distinct clusters of *"More than permitted passengers"* and *"Customer-related issues."*
  * Customer cancellations are heavily influenced by logistical friction, primarily *"Driver is not moving towards pickup location"* and *"AC is not working."*
  * **VTAT Correlation:** Vehicle Turnaround Time has a direct, visible mapping against high cancellation rates. `Prime Plus` exhibits the highest overall cancellation rate (~33.7%) tied directly to an elevated VTAT score.

### 4. Ratings & Sentiment Distribution
*Analysis of quality control, user satisfaction, and operator performance loops.*

![Ratings](./Ratings.png)

* **Visual Elements:** Driver Rating Distribution Histogram, Avg Rating by Vehicle Type, and Dual-Axis Customer vs. Driver Rating Daily Trend.
* **Key Insights:**
  * A massive, distinct cluster of **0-star ratings (~16K bookings)** indicates a heavy footprint of cancelled or failed rides where ratings default or reflect extreme customer dissatisfaction.
  * Successful ride ratings are tightly packed between **3.0 and 5.0 stars**.
  * Average driver ratings across active vehicle types remain strictly clustered around **2.65 to 2.70**, signaling a systemic need for driver behavior training or ride-quality audits.

### 5. Revenue Stream Analytics
*Financial evaluation tracking payment pipelines, vehicle yields, and daily revenue generation.*

![Revenue](./Revenue.png)

* **Visual Elements:** Payment Method Segmented Bar Chart by Vehicle Type, Stacked Daily Revenue Waveform ("Jan 2024").
* **Key Insights:**
  * Revenue streams are equally distributed across all vehicle verticals (Bikes, Sedans, Prime Plus, Autos), indicating a healthy diversified portfolio where low-ticket high-volume rides (Bikes/Autos) balance high-ticket lower-volume rides (SUVs/Prime).
  * **The Day 31 Drop:** The daily revenue trend tracks consistently around **₹1.1M to ₹1.2M daily** before experiencing an aggressive drop-off on Day 31, isolating a data ingestion truncation or an operational reporting cutoff that requires backend investigation.

---

## 📈 Strategic Business Recommendations
1. **Optimize VTAT in High-Premium Segments:** Focus operational efficiency on `Prime Plus` and `Prime SUV` lines. Reducing vehicle turnaround times in these categories will directly suppress their industry-high cancellation rates (~33.7%).
2. **Resolve "Driver Not Moving" Friction:** Implement stricter automated app pings or algorithmic reassignment windows for drivers who remain stationary post-acceptance, directly mitigating the top customer cancellation driver.
3. **Address the Zero-Rating Surge:** Isolate the 16K zero-rating spike to confirm whether it is an application logging bug for unfulfilled rides or genuine customer backlash due to driver behavior and mechanical failure (e.g., faulty ACs).

---

## 🚀 How to Run this Project Locally
1. Clone this repository to your local machine.
2. Open the `.pbix` file inside **Microsoft Power BI Desktop**.
3. Ensure you have the underlying Kaggle CSV dataset connected if you wish to refresh the data model mesh.
4. If checking backend queries, look at the included `.sql` script files for raw transformation queries.
