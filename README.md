# Denial Prevention Engine

## 1. Executive Summary

The **Denial Prevention Engine** is a healthcare claims intelligence product designed to identify claims that may be at elevated risk of denial **before submission or adjudication**, explain the factors driving that risk, and provide evidence-based guidance on what should be reviewed or corrected.

The product combines:

* CPT/HCPCS procedure codes
* ICD-10 diagnosis codes
* Modifiers
* Place of service
* Provider information
* Payer information
* Prior authorization information
* Coverage policies
* Historical claims
* Historical denial outcomes
* Procedure/diagnosis relationships
* Provider billing patterns
* Coding relationships
* Machine-learning risk models
* LLM-generated explanations

The core user question is:

> **"What could cause this claim to be denied, and what can I verify or correct before it is submitted?"**

The product should move beyond simply predicting:

> **"This claim has a 78% probability of denial."**

Instead, it should answer:

> **"This claim has several characteristics associated with historical denials. Here is the evidence, the likely risk category, and what should be verified before submission."**

The long-term vision is a **pre-submission claim intelligence layer** that connects procedure, diagnosis, payer policy, provider behavior, authorization, and historical denial patterns.

---

# 2. Product Concept

## Product Name

**Denial Prevention Engine**

Possible portfolio-facing name:

> **Denial Intelligence**

### Tagline

> **Identify claim risk before the denial.**

Alternative:

> **Turn historical denials into actionable pre-submission intelligence.**

---

# 3. Problem Being Solved

A traditional denial workflow is often reactive:

```text
Claim submitted
      ↓
Payer adjudication
      ↓
Claim denied
      ↓
Analyst investigates
      ↓
Denial reason identified
      ↓
Correction / appeal
```

The Denial Prevention Engine reverses that workflow:

```text
Claim prepared
      ↓
Risk assessment
      ↓
Potential denial reasons
      ↓
Evidence
      ↓
Recommended verification
      ↓
Claim corrected
      ↓
Claim submitted
```

The goal is to move part of denial management **upstream**.

---

# 4. Core User Question

The application should answer:

> **"If I submit this claim as currently configured, what should I investigate before submission?"**

For example:

```text
Claim
CPT: XXXXX
ICD-10: XXXXX
Modifier: XX
POS: 11
Provider: XXXXX
Payer: XXXXX
PA: Not identified
```

The engine might return:

```text
DENIAL RISK: HIGH

Potential issues:

1. Procedure/diagnosis combination differs from historical
   paid claims.

2. A payer policy associated with the procedure identifies
   diagnosis criteria that should be verified.

3. The claim is missing an authorization reference.

4. Modifier usage differs from the provider's historical
   paid claims.

5. Similar claims have historically been denied for
   coverage-related reasons.

Recommended review:
□ Verify diagnosis
□ Verify authorization
□ Verify modifier
□ Review payer policy
```

The system should not claim that the claim **will** be denied.

---

# 5. Target Users

Initial users:

* Claims analysts
* Revenue-cycle analysts
* Healthcare data analysts
* Health-plan analysts
* Provider billing teams
* Coding analysts
* Prior-authorization teams
* Denial management teams
* Product managers
* Utilization-management analysts

Future users:

* Health plans
* Provider organizations
* RCM companies
* Clearinghouses
* TPA organizations

---

# 6. Product Scope

The POC should focus on six capabilities.

## 1. Denial Risk Detection

Identify claims with characteristics associated with historical denials.

## 2. Denial Reason Prediction

Identify likely denial categories.

## 3. Policy Intelligence

Determine whether the claim appears consistent with available coverage and authorization policies.

## 4. Coding Intelligence

Evaluate procedure, diagnosis, modifier, and place-of-service relationships.

## 5. Provider Intelligence

Compare the claim against the provider's historical behavior and relevant peers.

## 6. Prevention Guidance

Explain what should be reviewed before submission.

---

# 7. The Most Important Product Design Principle

The product should not stop at:

> **"High denial risk."**

It should answer:

> **"Why?"**

and then:

> **"What can I do about it?"**

The workflow becomes:

```text
Risk
 ↓
Reason
 ↓
Evidence
 ↓
Action
```

This is what turns a prediction model into a **denial prevention product**.

---

# 8. Product Architecture

```text
                     CLAIM
                       |
        ┌──────────────┼───────────────┐
        |              |               |
    Procedure       Diagnosis       Provider
        |              |               |
     Modifier       Payer          POS
        |              |               |
        └──────────────┼───────────────┘
                       |
                 Authorization
                       |
                       ▼
              Historical Claims
                       |
                 Historical
                   Denials
                       |
                       ▼
              Policy Intelligence
                       |
          ┌────────────┼────────────┐
          |            |            |
       Coding       Coverage     Utilization
        Risk           Risk         Risk
          |            |            |
          └────────────┼────────────┘
                       |
                 ML Risk Engine
                       |
                Denial Categories
                       |
                    Evidence
                       |
                       ▼
                      LLM
                       |
                       ▼
              Prevention Guidance
                       |
                       ▼
                  Streamlit
```

---

# 9. Technology Stack

Continue using the same local architecture.

| Component          | Technology                |
| ------------------ | ------------------------- |
| Programming        | Python                    |
| Data manipulation  | pandas                    |
| Storage            | Parquet                   |
| Analytical queries | DuckDB                    |
| Machine learning   | scikit-learn              |
| Visualization      | Plotly                    |
| Application        | Streamlit                 |
| APIs               | requests                  |
| AI                 | LLM API                   |
| Version control    | Git/GitHub                |
| Deployment         | Streamlit Community Cloud |

No Snowflake is required for the POC.

---

# 10. Data Sources

The Denial Prevention Engine requires several data domains.

## Claims

* Procedure
* Diagnosis
* Modifier
* Place of service
* Provider
* Payer
* Service date
* Units
* Charges
* Allowed amount
* Paid amount
* Claim status

## Denials

Ideally:

* Denial code
* Denial category
* Denial reason
* Claim
* Claim line
* Payer
* Procedure
* Diagnosis
* Provider
* Date
* Appeal status
* Resolution

## Procedure

* CPT
* HCPCS
* Procedure category
* Effective dates

## Diagnosis

* ICD-10-CM
* Diagnosis categories
* Effective dates

## Coverage

* NCD
* LCD
* Articles
* Payer medical policies

## Authorization

* Authorization requirement
* Authorization number
* Authorization status
* Authorization date
* Authorized procedure
* Authorized units

---

# 11. Relationship to Your Existing Projects

This product should reuse the intelligence layers you have already designed.

```text
CPT/HCPCS Procedure Intelligence
              ↓
       Coverage Intelligence
              ↓
   Prior Authorization Engine
              ↓
      Denial Prevention Engine
```

The Denial Prevention Engine becomes the downstream intelligence layer.

The overall ecosystem becomes:

```text
Procedure
    ↓
Coverage
    ↓
Prior Authorization
    ↓
Claim
    ↓
Denial Risk
    ↓
Prevention
```

---

# 12. Critical Data Limitation

Your CMS synthetic claims data does **not** provide a realistic labeled denial dataset sufficient to train a real-world denial prediction model.

Therefore, the POC should use two different datasets.

## Dataset 1 — Synthetic CMS Claims

Use for:

* Procedure intelligence
* Diagnosis relationships
* Provider utilization
* Place of service
* Historical claim patterns

## Dataset 2 — Synthetic Denial Dataset

Create a synthetic dataset containing:

```text
claim_id
claim_line_id
denial_code
denial_category
denial_reason
denial_date
payer
procedure
diagnosis
provider
modifier
place_of_service
authorization_status
```

Clearly label it:

> **Synthetic demonstration data — not representative of actual payer denial behavior.**

This allows you to demonstrate the architecture without pretending that synthetic data produces validated real-world denial predictions.

---

# 13. Denial Taxonomy

A major component of the product should be a standardized denial taxonomy.

For example:

```text
DENIAL
│
├── Authorization
│
├── Eligibility
│
├── Coverage
│
├── Medical Necessity
│
├── Coding
│   ├── Invalid Code
│   ├── Modifier
│   ├── Procedure/Diagnosis
│   └── Duplicate
│
├── Documentation
│
├── Timely Filing
│
├── Provider
│
├── Place of Service
│
└── Other
```

The exact categories should ultimately be based on the actual denial codes available in the dataset.

---

# 14. Denial Data Model

Create:

## `denial_df`

```text
claim_id
claim_line_id
denial_code
denial_category
denial_reason
payer_id
procedure_code
diagnosis_code
provider_id
modifier
place_of_service
authorization_status
denial_date
resolution_status
```

This becomes the foundation for the ML model.

---

# 15. Claim Data Model

Create a canonical claim representation.

## `claim_df`

```text
claim_id
claim_line_id
service_date
payer_id
provider_id
procedure_code
code_system
diagnosis_1
diagnosis_2
diagnosis_3
diagnosis_4
modifier_1
modifier_2
place_of_service
units
charge_amount
allowed_amount
paid_amount
authorization_id
claim_status
```

---

# 16. Denial Feature Dataset

Create:

## `denial_features_df`

Each row represents a claim or claim line.

Potential features:

```text
procedure_code
diagnosis_code
modifier
place_of_service
provider_id
payer_id
authorization_status
procedure_volume
provider_procedure_rate
procedure_diagnosis_frequency
peer_procedure_rate
historical_denial_rate
provider_denial_rate
payer_denial_rate
procedure_denial_rate
diagnosis_denial_rate
modifier_denial_rate
pos_denial_rate
```

---

# 17. Historical Denial Features

Historical denial behavior will be particularly important.

Calculate:

### Procedure denial rate

```text
Denied claims for procedure
---------------------------
All claims for procedure
```

### Provider denial rate

```text
Denied claims for provider
--------------------------
All claims for provider
```

### Payer denial rate

```text
Denied claims for payer
-----------------------
All claims for payer
```

### Procedure/diagnosis denial rate

```text
Denied claims for procedure + diagnosis
---------------------------------------
All claims for procedure + diagnosis
```

These should be calculated using an appropriate historical window to avoid leakage.

---

# 18. Avoiding Data Leakage

This is one of the most important technical requirements.

When predicting whether a claim will be denied, the model cannot use information that becomes available **after the claim was adjudicated**.

For example, do not use:

```text
denial_code
denial_date
appeal_result
final claim status
```

as predictive features.

Instead:

```text
Historical information
          ↓
Claim submitted
          ↓
Prediction
          ↓
Actual denial
```

The model should only see information available at the prediction point.

---

# 19. Prediction Target

The first ML target should be:

```text
DENIED = 1
PAID = 0
```

A second model can predict:

```text
denial_category
```

For example:

```text
Authorization
Coding
Coverage
Documentation
Medical Necessity
```

The second problem is a multiclass classification problem.

---

# 20. Model Architecture

Use two stages.

## Stage 1 — Denial Risk

```text
Claim
 ↓
Probability of denial
```

## St
