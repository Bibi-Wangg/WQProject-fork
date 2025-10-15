# Water Quality Project

## Table of Contents
- [Abstract](#abstract)
- [Problem Formulation](#problem-formulation)
- [Datasets](#datasets)
- [Evaluation Metrics](#evaluation-metrics)
- [Missing Data Patterns](#missing-data-patterns)
---

## Abstract

This project addresses the critical challenge of missing data in water quality monitoring systems through a cross-variable spatial-temporal imputation framework. The approach leverages knowledge transfer from densely-observed source variables to reconstruct sparsely-observed target variables, exploiting their shared sensor network topology while accounting for distinct temporal dynamics.

### Key Innovation
Unlike traditional single-variable imputation methods, this framework:
- **Leverages cross-variable relationships** between related water quality indicators
- **Exploits shared spatial topology** of monitoring networks
- **Aligns temporal dynamics** across variables with different characteristics
- **Handles severe missingness** through knowledge transfer from better-observed variables

---

## Problem Formulation

### Cross-Variable Spatial-Temporal Imputation Task

**Definition**: Given a sparsely observed target variable, reconstruct its missing values by transferring knowledge from a related, better-observed source variable, where both variables are measured at the same set of monitoring stations.

### Mathematical Formulation

#### Spatial Structure
Sensor network represented as **G = (V, E, A)** where:
- **V = {v₁, ..., vₙ}**: Set of N monitoring stations
- **E**: Connections in the sensor network (e.g., stream flow connections)
- **A ∈ ℝ^(N×N)**: Adjacency matrix encoding spatial relationships

#### Variable Representation
For each variable **p ∈ {Source, Target}**:
- **X_p ∈ ℝ^(N×L)**: Spatial-temporal measurements
  - N = number of stations
  - L = number of timestamps
  - Each entry X_p[n,l] represents measurement at station n and time l
  
- **M_p ∈ {0,1}^(N×L)**: Observation mask
  - M_p[n,l] = 1 if value is observed
  - M_p[n,l] = 0 if value is missing

#### Terminology Clarification
- **Variable**: One measurable quantity (e.g., discharge, nitrate concentration)
- **Multivariate Time Series**: For each variable, measurements from N stations form N parallel univariate time series
- **Station**: Physical monitoring location where multiple variables are measured

#### Objective
Impute missing values in target variable **X̃_Target** by leveraging:
1. Observed values in X_Target (where M_Target = 1)
2. Information from related source variable X_Source
3. Shared spatial network structure A
4. Temporal patterns in both variables

---

## Datasets

### Data Source
**U.S. Geological Survey (USGS)** monitoring stations in the **Western Lake Erie Basin, Maumee River Watershed**

- **Portal**: [USGS Water Quality Data Portal](https://www.waterqualitydata.us/)
- **Region**: Western Lake Erie Basin, Ohio, USA
- **Network**: Stream and river monitoring stations with known topology

---

### Dataset 1: Q→SSC (Discharge to Suspended Sediment Concentration)

#### Physical Relationship
River discharge (flow rate) strongly influences sediment transport capacity and concentration. Higher discharge events mobilize and suspend more sediment particles.

#### Dataset Characteristics

| Characteristic | Source Variable: Q (Discharge) | Target Variable: SSC |
|---|---|---|
| **Monitoring Period** | April 15, 2015 - September 30, 2022 | April 15, 2015 - September 30, 2022 |
| **Number of Stations** | 20 USGS stations | 20 USGS stations |
| **Total Samples** | 2,726 time windows | 2,726 time windows |
| **Temporal Resolution** | Daily measurements | Daily measurements |
| **Window Length** | 16 consecutive days | 16 consecutive days |
| **Original Missing Rate** | 0% (densely observed) | 19.99% (sparse observations) |
| **Physical Unit** | Cubic feet per second (cfs) | Milligrams per liter (mg/L) |
| **Measurement Method** | Automated stream gauges (continuous) | Manual sampling + lab analysis (intermittent) |

#### Scientific Motivation
- **Source (Discharge)**: Continuously measured via automated stream gauges at minimal cost
- **Target (SSC)**: Requires expensive manual water sampling and laboratory analysis
- **Missing Data Cause**: Cost constraints, accessibility issues, weather conditions
- **Application**: Critical for sediment load estimation, erosion assessment, and watershed management

---

### Dataset 2: NH4→NO3 (Ammonia to Nitrate Nitrogen)

#### Chemical Relationship
Both are forms of inorganic nitrogen involved in the nitrogen cycle. Ammonia (NH₄⁺) is converted to nitrate (NO₃⁻) through nitrification, a microbial oxidation process. Both forms respond to similar environmental drivers (temperature, flow, agricultural inputs).

#### Dataset Characteristics

| Characteristic | Source Variable: NH4 (Ammonia) | Target Variable: NO3 (Nitrate) |
|---|---|---|
| **Monitoring Period** | April 15, 2015 - September 30, 2024 | April 15, 2015 - September 30, 2024 |
| **Number of Stations** | 12 USGS stations | 12 USGS stations |
| **Total Samples** | 3,457 time windows | 3,457 time windows |
| **Temporal Resolution** | Daily measurements | Daily measurements |
| **Window Length** | 16 consecutive days | 16 consecutive days |
| **Original Missing Rate** | 10.24% | 10.24% |
| **Physical Unit** | mg/L as N | mg/L as N |
| **Measurement Method** | Laboratory analysis | Laboratory analysis |

#### Scientific Motivation
- **Nitrogen Pollution**: Critical indicators of agricultural runoff and urban pollution
- **Biogeochemical Linkage**: Shared transformation pathways in nitrogen cycle
- **Water Quality**: Excessive nitrogen causes eutrophication and harmful algal blooms
- **Application**: Nutrient load modeling, source tracking, water quality assessment

---

### Dataset 3: SRP→TP (Orthophosphate to Total Phosphorus)

#### Chemical Relationship
Soluble Reactive Phosphorus (SRP, orthophosphate PO₄³⁻) is the dissolved, bioavailable component of Total Phosphorus (TP). TP includes both dissolved and particulate forms. SRP represents the immediately usable fraction for algal growth.

#### Dataset Characteristics

| Characteristic | Source Variable: SRP (Orthophosphate) | Target Variable: TP (Total Phosphorus) |
|---|---|---|
| **Monitoring Period** | April 15, 2015 - September 30, 2024 | April 15, 2015 - September 30, 2024 |
| **Number of Stations** | 12 USGS stations | 12 USGS stations |
| **Total Samples** | 3,457 time windows | 3,457 time windows |
| **Temporal Resolution** | Daily measurements | Daily measurements |
| **Window Length** | 16 consecutive days | 16 consecutive days |
| **Original Missing Rate** | 10.24% | 10.25% |
| **Physical Unit** | mg/L as P | mg/L as P |
| **Measurement Method** | Colorimetric analysis (simpler) | Digestion + colorimetric analysis (complex) |

#### Scientific Motivation
- **Phosphorus as Limiting Nutrient**: Often controls algal growth in freshwater systems
- **Eutrophication**: Excess phosphorus causes harmful algal blooms, hypoxia, ecosystem degradation
- **Measurement Complexity**: TP analysis requires sample digestion; SRP analysis is simpler
- **Application**: Lake Erie water quality management, harmful algal bloom prediction

---

### Dataset Statistics Summary

| Dataset | Samples | Window Length | Stations | Source Missing Rate | Target Missing Rate |
|---------|---------|---------------|----------|---------------------|---------------------|
| Q→SSC | 2,726 | 16 days | 20 | 0% | 19.99% |
| NH4→NO3 | 3,457 | 16 days | 12 | 10.24% | 10.24% |
| SRP→TP | 3,457 | 16 days | 12 | 10.24% | 10.25% |

### Data Preprocessing

#### Training/Validation/Test Split
- **Training Set**: 70% of temporal data
- **Validation Set**: 10% of temporal data  
- **Test Set**: 20% of temporal data
- **Split Method**: Temporal split (no data leakage across time)
---

## Evaluation Metrics

### 1. RMSE (Root Mean Square Error)

**Formula**: 
```
RMSE = sqrt(mean((y_true - y_pred)²))
```
---

### 2. MAE (Mean Absolute Error)

**Formula**: 
```
MAE = mean(|y_true - y_pred|)
```
---

### 3. CRPS (Continuous Ranked Probability Score)

**Formula**: 
```
CRPS = ∫(F(x) - 1{x ≥ y_true})² dx
```
where F(x) is the cumulative distribution function of predictions

---

## Missing Data Patterns

The evaluation considers two realistic missingness patterns:

### 1. Point Missing (Random Missingness)

**Characteristics**:
- **Pattern**: 20% of observed values randomly masked
- **Distribution**: Uniform random across space and time
- 
---

### 2. Block Missing (Consecutive Missingness)

**Characteristics**:
- **Pattern**: 
  - Continuous intervals of length [L/2, 2L](8-32 days) where L = 16 masked with 0.15% probability per station
  - Plus additional 5% random point masking

---
