================================================================================
           CAPSTONE PROJECT CHARTER & DATA REQUIREMENT SPECIFICATION
              CRISP-DM STAGE 1: BUSINESS UNDERSTANDING & PROBLEM FRAMING
================================================================================

PROJECT TITLE: Wind Portfolio Performance Optimization & Generation Gap Recovery
PORTFOLIO CAPACITY: 1 GW Synthetic Wind Portfolio (~3.33 TWh/year @ 38% CF)
TARGET OBJECTIVE: Recover 2–3% Annual Energy Production (66.6 – 99.9 GWh/year)

================================================================================
1. EXECUTIVE SUMMARY & QUANTIFIED PROBLEM STATEMENT
================================================================================

Wind power generation across the 1 GW fleet exhibits significant variability driven 
by meteorology, turbine health, grid curtailment, and control settings. Operational 
analysis indicates:

* Baseline Portfolio Capacity Factor (CF): ~38%
* Underperforming Turbine-Hours: ~14% of operational hours produce >=10% less 
  power than theoretical expectations under comparable meteorological conditions.
* Annual Generation Gap: Estimated 5–8% loss in total potential energy.
* Financial & Energy Recovery Target: Recapturing 2–3% of lost Annual Energy 
  Production (AEP) translates to 66.6–99.9 GWh/year of recovered energy.

Academic/Data Disclaimer:
The baselines and percentages referenced herein represent realistic synthetic 
assumptions for capstone modeling purposes and do not represent confidential 
operational figures of ACWA Power or any specific commercial operator.

================================================================================
2. KEY QUANTIFIED BASELINES & SUCCESS CRITERIA
================================================================================

+----------------------------------+-------------------------------------------+
| Metric                           | Target / Baseline                         |
+----------------------------------+-------------------------------------------+
| Portfolio Capacity Factor        | ~38%                                      |
| Underperforming Turbine-Hours    | ~14% generating >10% below expected output|
| Annual Generation Gap            | ~5–8% total annual revenue/MWh loss       |
| Expected Power Model Accuracy    | R² >= 0.90 (Regression target)            |
| Business Recovery Target         | Recover >= 2–3% AEP (66.6–99.9 GWh/year)  |
+----------------------------------+-------------------------------------------+

================================================================================
3. CORE PROJECT OBJECTIVES
================================================================================

1. Predict Expected_Power_kW and short-horizon generation (1h, 6h, 24h).
2. Classify Underperformance_Flag when actual output drops >10% below expected 
   power under eligible operating conditions.
3. Decompose & Disambiguate Losses: Separate environmental/weather effects, grid 
   curtailment, and maintenance outages from controllable technical losses.
4. Anomaly Detection: Detect subtle turbine component degradation (yaw, pitch, 
   gearbox, generator) using multi-sensor SCADA variables.
5. Actionable Prioritization: Rank turbines by recoverable lost MWh and potential 
   dollar revenue to optimize dispatch of maintenance resources.
6. Executive & Field Dashboard: Deliver an interactive performance monitoring 
   and decision-support system for operational leadership and site engineers.

================================================================================
4. TARGET VARIABLE DEFINITIONS
================================================================================

1. Primary Regression Target: `Expected_Power_kW`
   - Continuous power output (kW) predicted based on ideal power curves adjusted 
     for real-time ambient temperature, wind speed, wind direction, air density, 
     and turbulence intensity.

2. Secondary Classification Target: `Underperformance_Flag`
   - Binary indicator (1 / 0) defined as:
     
     Underperformance_Flag = 1 IF:
       (Expected_Power_kW - Actual_Power_kW) / Expected_Power_kW > 0.10
       AND Turbine_Available == True
       AND Grid_Curtailment_Flag == False
       AND Wind_Speed >= Cut_In_Wind_Speed (e.g., 3.0 m/s)
     ELSE 0

================================================================================
5. PROJECT TEAM & RESPONSIBILITIES
================================================================================

+----------------------------------+-------------------------------------------+
| Role                             | Core Responsibilities                     |
+----------------------------------+-------------------------------------------+
| Lead Data Scientist / PM         | Manages CRISP-DM lifecycle, aligns ML     |
|                                  | models with financial targets, validates  |
|                                  | R² >= 0.90 performance.                   |
| Wind Energy / Domain Engineer    | Defines aerodynamic & physical constraints,|
|                                  | validates power curve baselines, guides   |
|                                  | root-cause mapping for technical losses.  |
| Data Engineer                    | Builds SCADA data pipelines, enforces     |
|                                  | schema validation, handles missing data,  |
|                                  | computes derived atmospheric features.    |
| ML Engineer / MLOps              | Develops, tunes, deploys, and monitors    |
|                                  | regression/classification models; manages |
|                                  | model registry and drift detection.       |
| BI & Dashboard Developer         | Visualizes fleet health, builds turbine   |
|                                  | ranking interfaces, and formats actionable|
|                                  | reports for field operations.             |
+----------------------------------+-------------------------------------------+

================================================================================
6. STAKEHOLDER BUSINESS QUESTIONS (5x5 FRAMEWORK)
================================================================================

A. DESCRIPTIVE QUESTIONS (What happened?)
--------------------------------------------------------------------------------
1. What is the historic capacity factor and energy yield per turbine/site over 
   the last 12 months?
2. What fraction of total operating hours were impacted by >10% underperformance?
3. How are lost generation MWh distributed across wind farms, sub-regions, and 
   turbine OEM models?
4. What were the predominant environmental conditions (wind speed, temperature) 
   during severe generation deficit events?
5. What is the historic ratio of scheduled maintenance downtime versus 
   unplanned forced outages?

B. DIAGNOSTIC QUESTIONS (Why did it happen?)
--------------------------------------------------------------------------------
1. Which specific component anomalies (yaw offset, pitch error, gearbox temperature) 
   statistically correlate with the >10% power gap?
2. How much energy loss is attributable to controllable technical issues vs. 
   external factors (grid curtailment, wind shear, low resource)?
3. Are specific software versions or control settings causing premature pitching 
   or sub-optimal power tracking?
4. Is wake loss from neighboring upstream turbines disproportionately degrading 
   downstream performance in key wind vectors?
5. How much revenue is lost due to TSO-mandated grid curtailment vs. internal 
   derating caused by high component temperatures?

C. PREDICTIVE QUESTIONS (What will happen?)
--------------------------------------------------------------------------------
1. What is the expected fleet power output (kW) over the next 1-hour, 6-hour, 
   and 24-hour horizons?
2. Which specific turbines are at highest risk of triggering an 
   `Underperformance_Flag` in the upcoming 7 days?
3. Can early temperature drift and vibration signatures forecast major component 
   failures (e.g., gearbox, main bearing) 14–30 days in advance?
4. What is the projected cumulative energy deficit (MWh) over the next quarter if 
   current degradation trends are left unaddressed?
5. What is the probability of grid curtailment events occurring based on regional 
   weather forecasts and demand models?

D. PRESCRIPTIVE QUESTIONS (How can we make it happen?)
--------------------------------------------------------------------------------
1. Which specific turbines should field engineering prioritize this week to 
   maximize recoverable MWh and financial return?
2. What precise calibration adjustments (e.g., yaw alignment offset, pitch zero-point) 
   are required to restore nominal power curve performance?
3. What is the optimal maintenance schedule that minimizes lost generation revenue 
   by targeting low-wind forecast windows?
4. How should technical capital be allocated between software re-tuning vs. 
   physical component overhauls to achieve the 2–3% AEP recovery goal?
5. What automated threshold should trigger an immediate operational alert to prevent 
   a derated turbine from running indefinitely in a degraded state?

E. COGNITIVE QUESTIONS (How can the system adapt and learn?)
--------------------------------------------------------------------------------
1. How can the system dynamically adapt baseline power curves to account for 
   normal seasonal variation and long-term turbine aging?
2. Can the model autonomously distinguish between temporary environmental fouling 
   (e.g., blade icing, insect build-up) and permanent mechanical damage?
3. How can post-maintenance technician work orders and field feedback be fed back 
   into the model to improve diagnostic accuracy?
4. How can the AI automatically negotiate trade-offs between short-term revenue 
   generation and component lifespan extension (derated operation)?
5. How can multi-sensor SCADA anomaly patterns be converted into natural-language 
   action plans for field technicians?

================================================================================
7. ANALYTICAL BASE TABLE (ABT) SPECIFICATION FOR BUSINESS DATA REQUEST
================================================================================

To build, train, and validate the models necessary for achieving the project 
goals, the data science team requires access to historical 10-minute SCADA, 
meteorological, status, and maintenance data.

Target Grain: 1 Row per Turbine per 10-Minute Interval

+----------------------+--------------------+--------+-------------------------------------------------+
| Column / Feature     | Target Data Type   | Source | Description & Business Rationale                |
+----------------------+--------------------+--------+-------------------------------------------------+
| Timestamp            | DATETIME (UTC)     | SCADA  | 10-minute bucket timestamp (e.g., YYYY-MM-DD HH:MM)|
| Turbine_ID           | VARCHAR(50)        | SCADA  | Unique identifier for each wind turbine         |
| Wind_Farm_ID         | VARCHAR(50)        | SCADA  | Identifier for the wind farm asset              |
| Turbine_Model        | VARCHAR(50)        | Asset  | OEM model designation, rated capacity, rotor size|
| Latitude             | FLOAT              | GIS    | Geolocation latitude for wake/spatial analysis  |
| Longitude            | FLOAT              | GIS    | Geolocation longitude for wake/spatial analysis |
| Actual_Power_kW      | FLOAT              | SCADA  | Measured active power output (kW)               |
| Expected_Power_kW    | FLOAT              | Model  | Calculated/Target expected power output (kW)    |
| Wind_Speed_ms        | FLOAT              | SCADA  | Anemometer wind speed at nacelle height (m/s)   |
| Wind_Direction_deg   | FLOAT              | SCADA  | Wind direction relative to true north (0-360°)  |
| Nacelle_Position_deg | FLOAT              | SCADA  | Physical nacelle orientation relative to north  |
| Yaw_Offset_deg       | FLOAT              | Derived| Misalignment between wind direction & nacelle   |
| Pitch_Angle_deg      | FLOAT              | SCADA  | Blade pitch angle (degrees)                     |
| Rotor_RPM            | FLOAT              | SCADA  | Rotational speed of the turbine rotor (RPM)     |
| Generator_RPM        | FLOAT              | SCADA  | Rotational speed of the generator shaft (RPM)   |
| Ambient_Temp_C       | FLOAT              | Met    | Ambient air temperature (°C)                    |
| Air_Density_kgm3     | FLOAT              | Met/Der| Air density calculated from temp & pressure     |
| Barometric_Pressure  | FLOAT              | Met    | Atmospheric pressure (hPa)                      |
| Gearbox_Temp_C       | FLOAT              | SCADA  | Main gearbox oil/bearing temperature (°C)       |
| Generator_Temp_C     | FLOAT              | SCADA  | Generator winding temperature (°C)              |
| Nacelle_Temp_C       | FLOAT              | SCADA  | Temperature inside nacelle housing (°C)         |
| Availability_Flag    | BOOLEAN (0/1)      | SCADA  | 1 = Turbine mechanically available for power    |
| Curtailment_Flag     | BOOLEAN (0/1)      | SCADA  | 1 = Grid operator mandated power reduction      |
| Fault_Code           | VARCHAR(50)        | Alarm  | Active SCADA alarm or error code                |
| Grid_Frequency_Hz    | FLOAT              | SCADA  | Grid electrical frequency (Hz)                  |
| Maintenance_Status   | VARCHAR(50)        | CMMS   | Work order status (e.g., Active_PM, Forced_Out)|
| Lost_Energy_kWh      | FLOAT              | Derived| Calculated gap: max(0, Expected - Actual) * 1/6 |
| Underperformance_Flag| BOOLEAN (0/1)      | Target | 1 if gap > 10% under valid operating conditions |
+----------------------+--------------------+--------+-------------------------------------------------+

Data Volume & History Requirements:
- Minimum Historical Range: 24 Months of continuous 10-minute resolution data.
- Coverage: 100% of turbines within the target 1 GW portfolio.

================================================================================
8. END-TO-END POTENTIAL DEPLOYMENT PLAN
================================================================================

To ensure smooth transition from research to business operations, the project 
will execute across 4 deployment phases:

PHASE 1: Offline Model Validation & Proof-of-Concept (Months 1–2)
--------------------------------------------------------------------------------
* Train baseline models (XGBoost / LightGBM / Neural Nets) on historical ABT.
* Evaluate Expected_Power_kW regression against target R² >= 0.90.
* Validate Underperformance_Flag classification performance (Precision/Recall).
* Perform backtesting on historical data to verify recoverable MWh calculations.

PHASE 2: Pilot Deployment & Shadow Mode Testing (Months 3–4)
--------------------------------------------------------------------------------
* Deploy model pipeline in "Shadow Mode" (daily batch processing of live SCADA).
* Select 2 pilot wind farms (~200 MW total capacity) for initial field validation.
* Generate weekly pilot reports ranking underperforming turbines.
* Conduct field inspections with wind engineers to physically verify top 10 
  flagged root causes (e.g., pitch calibration, yaw misalignment).

PHASE 3: Operational Dashboard Integration & Workflows (Months 5–6)
--------------------------------------------------------------------------------
* Connect pipeline to real-time SCADA datalake (10-minute batch / stream ingestion).
* Launch Interactive Wind Portfolio Dashboard for site engineers and management.
* Integrate automated alerting into existing Computerized Maintenance Management 
  System (CMMS) to auto-generate work orders for high-loss turbines.
* Establish operational KPI tracking (recovered GWh/month vs. baseline).

PHASE 4: Full Portfolio Rollout & Continuous MLOps (Months 7+)
--------------------------------------------------------------------------------
* Scale automated pipeline across the full 1 GW portfolio.
* Implement automated model monitoring (detecting data drift, seasonal shifts, 
  and concept drift).
* Schedule automated monthly retraining cycles using fresh SCADA data.
* Establish feedback loops where field technician work-order resolution codes 
  re-train diagnostic classification algorithms.

================================================================================
                                END OF DOCUMENT
================================================================================
