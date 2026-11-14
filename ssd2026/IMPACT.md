# Impact Analysis

**Framing (read this first):** this model does not claim to have prevented
any crashes. It claims to **concentrate limited safety-intervention
resources onto the road conditions the data identifies as highest-risk,
more efficiently than treating all segments equally.** Everything below is
scenario-based potential impact under stated assumptions, not a causal or
retrospective claim. See `MODEL_VALIDATION.md` for why this dataset
(Kaggle Playground Series S5E10, synthetic) can't support a stronger claim
than that.

## 1. Risk concentration — the primary evidence

Ranking all 517,754 training rows by predicted risk score:

| Risk tier | % of segments | Mean predicted risk | Share of total risk mass | Concentration ratio |
|---|---:|---:|---:|---:|
| Top 1% | 1% | 0.784 | 2.2% | **2.23×** |
| Top 5% | 5% | 0.698 | 9.9% | **1.98×** |
| Top 10% | 10% | 0.643 | 18.2% | **1.82×** |
| Top 25% | 25% | 0.554 | 39.3% | **1.57×** |
| (overall mean) | 100% | 0.352 | 100% | 1.00× |

*Reproduce with `python3 compute_concentration.py`. An earlier version of this
table (2.50/2.22/1.93/1.64) could not be reproduced from model predictions: its
tier means matched the distribution of the actual target values rather than of
predictions, while being described as ranking on predictions. See
`CONCENTRATION.md`.*

Concentration ratio = share of total risk mass ÷ share of segments. A ratio
of 1.82 for the top 10% means: **the highest-risk tenth of road conditions
in this dataset carries about 1.8× its proportional share of total
predicted risk.** That's the number that says "this model finds usable
concentration, not noise" — stronger evidence for judges than R² alone,
because it demonstrates decision usefulness, not just fit.

(Note: this dataset has one continuous `accident_risk` target, not a
separate severity/injury label — so this is risk-mass concentration, not
"% of severe crashes," and is described that way throughout. Don't let the
submission text drift into claiming the latter.)

## 2. Scenario-based potential benefit

Chain: **model → risk ranking → high-risk segment identification → targeted
intervention → assumed effectiveness → scenario range.** Each link is
labeled; nothing here is asserted as measured fact.

Published engineering countermeasures (signage, lighting upgrades, curve
treatment) have documented crash-reduction effects in the 15–30% range at
treated sites (FHWA Crash Modification Factors Clearinghouse — a repository
of 3,000+ peer-reviewed countermeasure studies). We use three effectiveness
scenarios against the predicted risk mass held by the top-10% tier (18.2% of
the total, from section 1). If a treatment removed a given share of the
risk mass inside that tier, the reduction in total predicted risk mass is
18.2% multiplied by that share:

| Scenario | Assumed effectiveness within the top-10% tier | Reduction in total predicted risk mass |
|---|---:|---:|
| Conservative | 15% | 2.7% |
| Moderate | 20% | 3.6% |
| Strong | 30% | 5.5% |

These are reductions in the model's predicted risk score on synthetic data,
not reductions in crashes, injuries or deaths. Converting them into real-world
outcomes would need observed crash data with exposure and a before-and-after
evaluation of actual treatments, which is the purpose of the proposed pilot.
An earlier version of this section used 19.3% for the tier share, applied
the share twice, and translated the result into fatalities; those figures are
withdrawn.

## 3. What this section deliberately does not claim

- Does not claim the model has prevented, or will provably prevent, any
  specific crash.
- Does not claim the R² of 0.88 is itself the impact — it's the
  precondition for the ranking in §1 being trustworthy.
- Does not use "cost per fatality" as the primary metric — crash-risk
  concentration (§1) is primary because it's directly computable from this
  model; the dollar figure is tertiary framing only.

Sources: WHO Global Status Report on Road Safety 2023
(who.int/publications/i/item/9789240086517); FHWA CMF Clearinghouse
(cmfclearinghouse.fhwa.dot.gov).
