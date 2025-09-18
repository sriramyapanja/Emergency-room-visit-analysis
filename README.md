# Emergency-room-visit-analysis
Emergency Room Visit Analysis (OpenEMR → MySQL → Python)
Goal: Turn raw OpenEMR exports into a relational database, then analyze ER visits to surface patterns in vitals, procedures, demographics, and follow-up prescriptions.
Stack: Python (pandas, mysql-connector-python), MySQL, Jupyter Notebook
What’s in this repo
er-visit-analysis.ipynb – full workflow: cleaning, schema build, inserts, analysis & visuals
er-visit-analysis_Presentation.pdf – short, visual summary of approach & findings
er-visit-analysis_ER_diagram
README.md – this file
Questions we asked
What are the most frequent reasons for ER visits?
Do abnormal vitals (temp, pulse, respiration) relate to hospitalization?
Which procedures are most common, and how do they vary by age group?
Are there demographic differences (sex, age) in visit frequency?
How often do ER patients receive follow-up prescriptions, and which meds top the list?
Key findings (at a glance)
Common visit reasons: general exams, well-child visits, symptom/check-ups, prenatal care.
Vitals & hospitalization: hospitalized patients showed more elevated pulse/respiration; suggests an association in this dataset.
Procedures: orders and lab tests dominate across all age groups.
Demographics: females > males across all age groups; highest volume in 51+ group (especially females).
Prescriptions: 97% received follow-ups; top meds included Acetaminophen, Amoxicillin-Clavulanate, Lisinopril, Naproxen, Hydrochlorothiazide. 
annotated-Final%20Presentation%…
Data pipeline 
Extract & Clean (Python)
Kept core fields (e.g., pid, fname, lname, dob, sex).
Introduced Location dimension (LocationID, city, state).
Linked Encounters, Vitals, Prescriptions, Procedures; dropped rows with missing essential vitals.
Relational Model (MySQL)
Normalized tables with PK/FK constraints:
Patient(pid, …, LocationID FK)
Location(LocationID, city, state)
Form_Encounter(encounter_id, pid FK, date, reason)
Vitals(encounter_id FK, temperature, pulse, respiration, …)
Prescription(encounter_id FK, rx_name, dose, …)
Procedure_Order(procedure_order_id, encounter_id FK, …)
Procedure_Report(procedure_report_id, procedure_order_id FK, …)
Load Order
Location → Patient → Form_Encounter → Vitals → Prescription → Procedure_Order → Procedure_Report.

How we answered the questions (Methodology)
Most frequent reasons – group counts from Form_Encounter.reason.
Vitals & hospitalization – define abnormal cutoffs (e.g., temp > 38°C, pulse > 100 bpm, resp > 24/min), compare distributions between admitted vs non-admitted encounters.
Procedures by age group – join Patient → Form_Encounter → Procedure_*; bucket ages (0–18, 19–35, 36–50, 51+).
Demographic differences – stratify counts by sex × age bucket.
Prescriptions – compute share of encounters with ≥1 Prescription and rank rx_name.
Results you can talk about in interviews
Built an extract-clean-load pipeline from OpenEMR exports into a normalized MySQL schema.
Enforced referential integrity and reproducible load order.
Framed clinical questions, then answered them with SQL + Python (group-bys, joins, rule-based flags, simple viz).
Produced a clear slide deck and a notebook that stakeholders can run end-to-end.
Notes on data access
If your OpenEMR exports are restricted, keep them out of the repo. Document:
Source system (OpenEMR)
Export date / cohort
Cleaning rules (what you kept, what you dropped, how you linked tables)
Contact
Sri Ramya Panja
Email: sriramyapanja123@gmail.com · LinkedIn: www.linkedin.com/in/sriramyapanja
