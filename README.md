# ER-Visits-Dashboard-


Project Overview
This project involves the development of a comprehensive healthcare analytics dashboard designed to monitor and optimize Emergency Room (ER) performance. By synthesizing over 9,200 patient records, the dashboard provides actionable insights into operational efficiency (wait times), patient demographics, and overall satisfaction scores. 

link to dashboard https://public.tableau.com/app/profile/tyrese.dieudonne/viz/HospitalEmergencyroomvisitDashboard_17777488311500/EmergencyRoomVisitsDS?publish=yes

<img width="962" height="669" alt="Screenshot 2026-05-02 at 3 27 11 PM" src="https://github.com/user-attachments/assets/6c1b77cd-6c0c-4597-93de-a65fb1ef9ee0" /> 


## Key Performance Indicators (KPIs)
*   **Total Patient Volume:** 9,216 visits analyzed.
*   **Average Wait Time:** 35.26 minutes.
*   **Patient Satisfaction:** 4.99/10 average score.
*   **Admission Rate:** ~50% of patients (4,612 admissions).

## Technical Stack
*   **Data Source:** `Hospital ER.csv`
*   **Tools:** Excel (Pivot Tables, Power Query), SQL (Data Aggregation)
*   **Visualization:** Heatmaps, Sparklines, and Categorical Bar Charts

## Data Analysis Highlights
### 1. Operational Efficiency (Wait Times)
I utilized a **Time-of-Day Heatmap** to identify critical staffing bottlenecks. The data revealed a significant spike in average wait times between **3:00 AM and 5:00 AM**, reaching peaks of nearly 40 minutes on certain weekdays. This insight allows hospital administrators to optimize graveyard shift staffing levels.

### 2. Patient Satisfaction Trends
By segmenting satisfaction scores by department and age group, the analysis identified that:
*   **Gastroenterology** maintained the highest satisfaction rate (5.8/10).
*   **Admitted patients** reported slightly higher satisfaction (5.1) compared to non-admitted patients (4.9).

### 3. Demographic Insights
*   The primary patient demographic falls within the **19-65 years** age bracket, accounting for 60% of all visits.
*   **General Practice** and **Orthopedics** are the highest-volume referral departments, managing nearly 3,000 patients combined.
