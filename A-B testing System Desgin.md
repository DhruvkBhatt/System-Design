# A/B Testing System Design: Customer Payment Risk Score Using GHOST-D

## Problem Statement

Collections teams need to decide which customers or invoices to follow up with first. The current process may be manual, rule-based, or based only on aging buckets.

We want to test whether a **Customer Payment Risk Score** helps collectors reduce overdue amount, reduce payment delay days, and improve collection efficiency.

The risk score is created using a two-model approach:

```text
Model 1: Classification → Predict probability of delay
Model 2: Regression → Predict expected delay days
```

Final score:

```text
Risk Score = P(delay) × Expected Delay Days × Invoice Amount × Cost Factor
```

The A/B test should answer:

> Does using the Customer Payment Risk Score improve collections outcomes compared with the existing process?

---

# GHOST-D Framework

```text
G — Goal
H — Hypothesis
O — Options
S — Sample
T — Tracking
D — Decision
```

---

## 1. G — Goal

The goal is not only to build an accurate ML model. The goal is to prove that using the score improves business outcomes.

**Business goals:**

* Reduce overdue amount
* Reduce average delay days
* Improve collection rate
* Improve collector productivity
* Avoid unnecessary customer friction

**Business decision:**

> Should collections teams use the Customer Payment Risk Score in their daily workflow?

---

## 2. H — Hypothesis

Convert the business goal into a measurable hypothesis.

```text
If collectors use the Customer Payment Risk Score to prioritize outreach,
then overdue amount and payment delay days will reduce compared with the existing process.
```

This makes the experiment measurable and business-focused.

---

## 3. O — Options

Define the two experiment groups.

| Group     | Experience                                         |
| --------- | -------------------------------------------------- |
| Control   | Existing collections prioritization process        |
| Treatment | Risk-score-based prioritized customer/invoice list |

**Treatment group receives:**

* Customer risk score
* Invoice-level risk score
* Priority ranking
* Reason codes
* Suggested action
* Daily refreshed list

Example:

```text
High risk: Contact today
Medium risk: Monitor this week
Low risk: No immediate action
```

---

## 4. S — Sample

Define who enters the experiment and how they are split.

### Eligibility

Include:

* Active customers
* Open invoices
* Valid payment terms
* Customers assigned to collections team
* Customers with enough historical payment data

Exclude:

* Legal cases
* Fraud cases
* Disputed invoices
* Strategic accounts needing manual handling
* Customers already on special payment plans
* Very new customers with no history

### Randomization Unit

Avoid invoice-level randomization because one customer can have multiple invoices. If one invoice is in treatment and another is in control, collector action may affect both.

Better randomization:

```text
Customer-level or portfolio-level randomization
```

Strong interview line:

> I would randomize at customer or portfolio level, not invoice level, to avoid contamination across invoices from the same customer.

### Stratification

Before randomization, balance control and treatment groups by:

* Region
* Customer segment
* Invoice amount
* Historical delay rate
* Aging bucket
* Payment terms
* Collector/team

---

# A/B Test Design Flow

```text
Eligible Customers / Portfolios
            ↓
Stratify by region, segment, invoice amount, delay history
            ↓
Random Assignment
       ┌───────────────┴───────────────┐
       ↓                               ↓
Control Group                    Treatment Group
──────────────                   ────────────────
Existing process                 Risk score priority list
       ↓                               ↓
Collectors act normally          Collectors follow risk ranking
       ↓                               ↓
Track payment outcomes           Track payment outcomes
       └───────────────┬───────────────┘
                       ↓
Compare results:
Overdue amount, delay days, collection rate, complaints
                       ↓
Decision:
Launch / rollback / iterate
```

---

## 5. T — Tracking

Define metrics, logging, and duration.

### Primary Metric

Pick one main metric before the test starts:

```text
Reduction in overdue amount
```

or

```text
Dollars collected earlier
```

These are strong because they directly connect to business value.

### Secondary Metrics

* Average delay days
* Collection rate
* DSO improvement
* Payment closure time
* Aging bucket movement
* Promise-to-pay conversion
* Collector productivity

### Guardrail Metrics

* Customer complaints
* Unnecessary follow-ups
* Dispute rate
* Collector workload
* False positive outreach
* High-value customer friction

Important principle:

> Do not launch only because collections improved. Launch only if collections improve and guardrails remain healthy.

### Duration

Payment behavior is slower than clickstream behavior, so the experiment should run long enough to observe payment outcomes.

Recommended duration:

```text
30 to 90 days
```

---

## 6. D — Decision

Compare treatment vs control and decide whether to launch, roll back, or iterate.

### Core Analysis

```text
Treatment outcome - Control outcome
```

Example:

```text
Overdue amount reduction in treatment - overdue amount reduction in control
```

Check:

* Absolute lift
* Relative lift
* Confidence interval
* Business impact
* Guardrail health
* Segment-level performance
* Data quality
* Collector adoption rate

### Decision Rule

| Result                                                   | Decision                             |
| -------------------------------------------------------- | ------------------------------------ |
| Primary metric improves and guardrails are healthy       | Launch                               |
| Primary metric improves but complaints/workload increase | Iterate                              |
| Result neutral but one segment improves                  | Run targeted test                    |
| Primary metric worsens                                   | Rollback                             |
| Data quality issue exists                                | Rerun experiment                     |
| Adoption is low                                          | Improve workflow/training and retest |

---

# If A/B Testing Is Not Possible

If randomization is hard in collections, use causal alternatives:

* Difference-in-differences
* Matched control portfolios
* Phased rollout
* Propensity score matching

Example:

```text
Impact =
(Treatment after - Treatment before)
-
(Control after - Control before)
```

---