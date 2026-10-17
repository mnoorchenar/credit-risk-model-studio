# Credit Risk Model Studio: Beginner's Guide

A compact explanation of everything in the app, in plain language.

## 1. The big picture

**What the app does:** it predicts which mortgage borrowers are likely to **default** (stop paying), explains *why*, and turns that into approve/decline decisions. It also checks whether the model is safe to use. It all runs in your browser, and **all data is fake (synthetic)**.

**Typical bank workflow = the 7 tabs:**

| Tab | Question it answers |
|---|---|
| 1 Data | Is my data clean? |
| 2 Train | Can I build a model that predicts default? |
| 3 Evaluate | Is the model any good? |
| 4 Explain | Why does it say that? |
| 5 Decide | How do I turn scores into decisions? |
| 6 Govern | Would it pass an independent audit? |
| 7 Copilot | Summarise it for executives, and answer questions. |

Plus a **chat bubble** (bottom right) on every page that answers questions about what is on screen.

## 2. Core terms

- **Default** – here: being **90+ days past due** on payments within the next 12 months.
- **PD (Probability of Default)** – the model's output: a number from 0% to 100% for each account. A PD of 3% means "about 3 in 100 such borrowers default".
- **LGD (Loss Given Default)** – if a borrower defaults, the share of the money the bank loses (e.g. 25%).
- **EAD (Exposure at Default)** – how much money is owed when default happens.
- **Expected loss** = PD × LGD × EAD.
- **Mortgage / HELOC** – a home loan / a credit line secured against your home equity.
- **Synthetic data** – made-up data that behaves realistically. No real customers.
- **Champion vs Challenger** – the *champion* is the current, simple, trusted model. The *challenger* is a newer, more complex model trying to beat it.
- **Vintage** – a group of loans started in the same period. "Recent vintage" = newest loans, where economic conditions differ from older ones.

## 3. The 15 features (the model's inputs)

| Feature | Meaning | Risk direction |
|---|---|---|
| Credit score | Bureau score, 300–900 | Higher = safer |
| Loan-to-value (LTV) | Loan ÷ home value | Higher = riskier (less owner equity) |
| Debt service ratio (TDS) | Share of income going to debt payments | Higher = riskier |
| Annual income | Household income, $k | Higher = safer |
| Loan amount | Outstanding balance, $k | Neutral |
| Contract rate | Current interest rate | Neutral |
| Months on book | Account age | Neutral (very new accounts are slightly riskier) |
| Renewal payment shock | Payment increase when the mortgage renews within 12 months | Higher = riskier |
| HELOC utilization | % of the home equity line used | Higher = riskier |
| Delinquencies (12m) | Late payments in the last year | More = riskier |
| Local unemployment | Unemployment rate where the borrower lives | Higher = riskier |
| Local house prices (12m) | Local price change over 12 months | Higher = safer |
| Mortgage insured | Default-insured (1 = yes) | Slightly safer |
| Self-employed | 1 = yes | Slightly riskier |
| Condominium | 1 = yes | Neutral |

"Risk direction" is the **expected business logic**. Governance checks that the model respects it.

## 4. Tab 1: Data

- **30,000 accounts** are generated, deliberately **dirty** (like real data).
- **7 integrity controls** hunt for problems:
  - **Missing values** – empty cells.
  - **Invalid codes** – impossible values (e.g. credit score outside 300–900, LTV or debt ratio out of range).
  - **Duplicate account IDs** – same account twice.
  - **Insured mortgage with LTV below 55%** – inconsistent (insurance is normally for high LTV).
  - **Loan more than 10× income** – implausible, flagged for review.
- **Cleaning** – invalid values become missing, then missing values are filled with the **median** (the middle value) **from the training set only**. Every fix is logged.
- **Data leakage** – accidentally letting information from the test data influence training, which makes results look better than reality. Using training-only medians avoids it.
- **Feature explorer** – pick any feature and see how default rate changes with it.

## 5. Tab 2: Train

**Two models are trained:**

- **Champion = logistic scorecard.** Gives each feature one weight (coefficient) and adds them up. Simple, transparent, what regulators know best.
- **Challenger = gradient boosting.** Builds many small decision trees, each correcting the previous ones' mistakes. Finds non-linear patterns and interactions, but is a "black box" and needs SHAP to explain it.

**Data splits** (each account is in only one):
- **Train** – the model learns from it.
- **Validation** – used to tune settings and decide when to stop.
- **Test** – untouched until the end; the honest exam.
- **Recent** – newest loans, used to test performance when conditions shift.

**Sliders (hyperparameters = settings you choose before training):**

| Setting | Meaning |
|---|---|
| Epochs (champion) | How many passes over the data. Too few = not learned yet. |
| Number of trees (challenger) | How many trees to build. |
| Learning rate | Size of each learning step. Small = slow and careful; large = fast but can overshoot. |
| L2 regularisation / L2 on leaf values | A penalty on big weights that keeps the model simple and stops memorising. |
| Tree depth | How many questions each tree can ask. Deeper = more complex. |
| Min. samples per leaf | A leaf must cover at least this much data, which prevents tiny, noisy rules. |
| Row subsample | Each tree sees only a random part of the data, which reduces overfitting. |
| Default class weight | Defaults are rare, so they are counted ×N more to make the model pay attention. |
| Early stopping patience | Stop training if validation loss hasn't improved for N rounds (0 = off). |

**Presets:**
- **Underfit / Barely trained** – too simple to learn the pattern.
- **Balanced / Converged** – sensible settings with early stopping.
- **Overfit** – deep trees, no penalty, which memorises the training data.

**Key concepts:**
- **Underfitting** – model too simple; bad on both training and new data.
- **Overfitting** – model memorised training noise; great on training, worse on new data. Spot it when **training loss keeps falling but validation loss rises**.
- **Learning curves** – live charts of loss and AUC per round, for train vs validation.
- **Loss (log-loss)** – a penalty score; lower is better. It punishes confident wrong predictions heavily.
- **Early stopping** – stop at the point where validation was best.

## 6. Tab 3: Evaluate (is it any good?)

| Metric | Plain meaning | Good value |
|---|---|---|
| **AUC** | Chance that a random defaulter is ranked riskier than a random non-defaulter. 0.5 = coin flip, 1.0 = perfect. | ≥ 0.75 (here ~0.78–0.85) |
| **Gini** | 2×AUC − 1. Same info, rescaled to 0–1. | Higher is better |
| **KS** | Biggest gap between the cumulative good and bad distributions. | Higher is better |
| **Brier score** | Average squared error of the predicted probabilities. | Lower is better |
| **Log-loss** | Probability-based error (see above). | Lower is better |

**Charts:**
- **ROC curve** – catch-rate vs false-alarm rate across all cutoffs; closer to the top-left is better.
- **Calibration** – do predicted PDs match reality? If the model says 5%, about 5% should default. Points on the diagonal = well calibrated.
  - **Calibration error (ECE)** – average gap between predicted and observed.
  - **Calibration slope** – ~1.0 is ideal; far from 1 means over- or under-confident.
- **Decile lift** – accounts are sorted by risk and cut into 10 groups. Decile 1 = riskiest 10%. **Lift** = its default rate ÷ the average default rate (e.g. 6× means 6 times riskier than average).
- **Score distribution** – defaulters should pile up on the high-PD side.
- **Operating point** – choose a PD cutoff (decline everyone above it). It shows the trade-off: more defaults caught vs more good customers turned away.

## 7. Tab 4: Explain (why did it say that?)

- **SHAP** – a method that splits each prediction into contributions from each feature ("credit score pushed risk up by +1.2, income pushed it down by −0.3"). Contributions **add up exactly** to the prediction.
- **TreeSHAP** – the exact, fast SHAP algorithm for tree models. The app checks it against slow **brute-force Shapley values** and they match.
- **Global importance**
  - **Mean |SHAP|** – average size of a feature's push. Bigger = matters more.
  - **Permutation importance** – shuffle one feature and see how much accuracy drops. Bigger drop = more important.
- **Beeswarm plot** – one dot per account per feature. Horizontal position = effect on risk; colour = the feature's value. A clean gradient means a simple, monotonic relationship.
- **Partial dependence (PDP)** – the average effect on PD as one feature changes.
- **ICE lines** – the same, drawn for individual accounts, to show variation.
- **Business-logic check** – confirms curves go the expected direction (e.g. higher LTV never lowers risk).
- **Waterfall (one decision)** – step-by-step bars from the average account's PD to this account's PD.
- **Reason codes / adverse-action reasons** – top factors raising risk, in the wording lenders must give a declined applicant.
- **Counterfactual recourse** – "what would need to change to flip the decision?" (e.g. "raise credit score by 25 points"). Only **actionable** features are suggested (score, LTV, TDS, income, HELOC use), since you can't change local unemployment.

## 8. Tab 5: Decide

- **Decision bands** based on two PD cutoffs:
  - **Auto-approve** – PD below cutoff 1.
  - **Manual review** – between the cutoffs; a human looks.
  - **Decline** – PD at or above cutoff 2.
- **Observed vs expected** – observed = what really happened in the test data; expected = what the model predicted. Close agreement means a trustworthy model.
- **Expected vs realised loss** – predicted dollar loss (PD × LGD × EAD) compared with actual loss, by band.
- **Approval vs bad rate curve** – approve the safest accounts first; a lower curve means better selection.
- **Bad rate** – share of approved accounts that default.
- **Swap-set analysis** – compares the model to the **current rules** (approve if score ≥ 680, LTV ≤ 80%, TDS ≤ 44%) at the **same approval rate**.
  - *Swap-in*: accounts the model approves but rules reject.
  - *Swap-out*: accounts rules approve but the model rejects.
  - It shows how many **defaults are avoided**, which is the business value.

## 9. Tab 6: Govern (would it pass validation?)

**Governance / model risk management** = an independent check that a model is safe before the bank relies on it. Each check gives ✅ Pass, ⚠️ Warn or ❌ Fail:

| Check | What it tests | Pass threshold |
|---|---|---|
| Discriminatory power | AUC and KS on unseen data | AUC ≥ 0.75 |
| Overfitting | Train AUC − validation AUC | gap ≤ 0.04 |
| Out-of-time | AUC drop on the recent vintage | drop ≤ 0.03 |
| Calibration | Error ≤ 1% and slope 0.85–1.15 | |
| Score stability (PSI) | Score distribution shift, train vs recent | PSI ≤ 0.10 |
| Monotonicity | Risk moves in the expected direction | No violations |
| Segment consistency | Fair approval and accuracy across employment, property type and region | ratio ≥ 0.80 |
| Explainability | SHAP adds up to the prediction | error < 1e-6 |
| Data integrity | All data issues fixed and logged | |

**Verdict:** any fail → *Not ready for deployment*; any warn → *Approve with conditions*; else → *Ready for deployment*.

**Other terms:**
- **PSI (Population Stability Index)** – how much a distribution has changed. <0.10 stable, 0.10–0.25 noticeable shift, >0.25 major shift.
- **Population drift** – PSI per feature, showing which inputs changed between old and recent loans.
- **Out-of-time test** – testing on a later period than training, closest to real deployment.
- **Approval ratio** – a segment's approval rate relative to the best segment; below 0.80 is a red flag.
- **Stress test** – sliders for **unemployment (+pts)**, **house prices (−%)** and **renewal interest rates (+bps)**. The portfolio is re-scored to see how PD and expected loss rise. A *bps* (basis point) is 0.01%, so 100 bps = 1%.
  - It is *mechanical re-scoring*, **not an economic forecast**.
- **Model card** – downloadable document summarising purpose, data, method, performance, checks and limitations.

## 10. Tab 7: Copilot and the chat assistant

- **Executive memo** – a short model-risk summary (purpose, performance, business value, governance, recommendation).
- **Q&A** – ask questions about the model.
- **Fact sheet** – the list of computed numbers the text is allowed to quote.
- **Guardrails** – an AI answer is **rejected** and replaced by a template if it quotes numbers not in the fact sheet or overstates the validation results (so the AI can't make up results).
- **Page-aware chat bubble** – each tab builds a fact sheet from what is on screen, ranks facts by relevance to your question and answers only from them. It remembers each page's conversation. **Reset** clears this page; **Clear all pages** clears everything. Out-of-scope questions get a polite scope message.
- **Connect AI (optional)** – paste a free Hugging Face token to let a language model (Qwen, Gemma, Llama, DeepSeek) write the text. The token stays in your browser and goes only to `router.huggingface.co`. Without it, everything still works using built-in templates.

## 11. Quick reference: the numbers to remember

- **AUC:** 0.5 useless, 0.7 okay, 0.8 good, 0.9+ excellent (suspicious for credit).
- **PSI:** < 0.10 fine, > 0.25 serious.
- **Gap between train and validation AUC:** keep it small (< 0.04).
- **Calibration slope:** aim for about 1.
- **Lift:** top decile should be several times the average.

## 12. Project files (for the curious)

- `index.html`, `styles.css` – page and styling.
- `js/data.js` – data generator, integrity checks and cleaning.
- `js/ml.js` – the models, metrics, SHAP and PSI written in plain JavaScript.
- `js/pipeline.js` – the train/validation/test split, presets and training.
- `js/explain.js` – importance, PDP, reason codes, recourse, decision bands, swap-set, stress test.
- `js/governance.js` – the 9 validation checks, fact sheet, memo and model card.
- `js/llm.js`, `js/chat.js`, `js/widget.js`, `js/pagefacts.js` – the AI and chat layer.
- `js/ui-*.js` – one file per tab.
- `tests/` – automated tests for the maths and AI guardrails.
