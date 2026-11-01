# LTV prediction calculator

**Source:** Adapty, [State of In-App Subscriptions 2026](https://adapty.io/state-of-in-app-subscriptions-report/), 2025 transactions, net of App Store commission and taxes, and the Adapty blog posts linked in each section. These are third-party industry benchmarks, not this app's data: say so when you quote one. Adapted from [adaptyteam/growth-expert-skill](https://github.com/adaptyteam/growth-expert-skill) under its MIT license (see `../NOTICE.md`).

Run this when someone wants to know whether their app will be profitable, or asks for LTV, revenue, ROAS or breakeven. With Xee connected, fill the inputs from Xee first (prices from `list_products`, viewers and purchasers from `get_paywall`'s `recent`) and ask only for what Xee does not have, such as installs and CPI.

### Step 1: Collect Inputs (Conversational)

Ask the user for these inputs. Use defaults where the user doesn't have data.

**Subscription plans** — for each plan the user offers, collect:
- Plan type: weekly, monthly, quarterly, annual, or lifetime
- Price (USD)
- Distribution %: what share of paid subscribers choose this plan (must total 100%)

**Retention multipliers** — cumulative renewals at each time horizon. If the user doesn't have retention data, use the default benchmarks below.

**Trial funnel** — ask whether the user offers free trials. If yes, collect:
- **Trial Start Rate** (install → trial start %). Default: 11.2% (global average)
- **Trial-to-Paid Rate** (trial → paid conversion %). Default: 27.8% (global average)

When trials are used, the effective CR to Purchase = Trial Start Rate × Trial-to-Paid Rate. This replaces the direct CR input.

**Other inputs:**
- **Monthly installs** (current or projected)
- **CR to Purchase** (install → paid conversion rate). Use this directly if user has no trial, or calculate from trial funnel above. Default: 6%
- **CPI** (cost per install from paid acquisition). Default: $2.50
- **Apple fee**: 15% (Small Business Program, <$1M/yr revenue) or 30%. Default: 15%

### Default Retention Benchmarks (Cumulative Renewals)

Use these when the user doesn't have their own retention data. These represent median performance across Adapty's dataset.

| Plan | 1mo | 3mo | 6mo | 1yr | 2yr | 3yr | 4yr |
|------|-----|-----|-----|-----|-----|-----|-----|
| Weekly | 2.0 | 4.5 | 7.0 | 10.0 | 13.0 | 15.0 | 16.0 |
| Monthly | 0.4 | 0.7 | 1.0 | 1.6 | 2.0 | 2.2 | 2.3 |
| Quarterly | — | 0.15 | 0.2 | 0.35 | 0.4 | 0.45 | 0.42 |
| Annual | — | — | — | 0.1 | 0.13 | 0.15 | 0.16 |

"Cumulative renewals" means: at 1 year, a monthly subscriber has renewed 1.6 times on average (including partial retention). So their total payments = 1 (initial) + 1.6 (renewals) = 2.6 payment periods.

For lifetime plans, there are no renewals — LTV equals the one-time price at all time horizons.

### Step 2: Run Calculations

Perform these calculations for each time horizon T = {7d, 1mo, 3mo, 6mo, 1yr, 2yr, 3yr, 4yr}:

**Effective CR to Purchase (if user has trials):**
```
CR_to_purchase = Trial_Start_Rate × Trial_to_Paid_Rate
```
Example: 11.2% trial start × 27.8% trial-to-paid = 3.11% effective CR. Use this value in all formulas below. Show this breakdown to the user so they can see which part of the funnel to optimize.

**Per-plan LTV (gross, before Apple fee):**
```
LTV_plan(T) = Price × (1 + cumulative_renewals_at_T)
```
For 7d: use cumulative_renewals = 0 (user just purchased, no renewals yet).
For lifetime plans: LTV = Price at all time horizons.

**Blended ARPPU (Average Revenue Per Paying User):**
```
Blended_ARPPU(T) = Σ [ LTV_plan_i(T) × distribution_i ]
```
Sum across all plans, weighted by each plan's distribution %.

**ARPU (net, after Apple fee):**
```
ARPU(T) = Blended_ARPPU(T) × (1 - apple_fee)
```

**Projected Revenue:**
```
Revenue(T) = Installs × CR_to_purchase × ARPU(T)
```

**Unit Economics:**
```
CAC = CPI / CR_to_purchase
ROAS(T) = (CR_to_purchase × ARPU(T)) / CPI
Breakeven = first T where ROAS ≥ 1.0
LTV_to_CAC = ARPU(4yr) / CAC
```

### Step 3: Present Results

Format results as a table:

```
| Metric         | 7d     | 1mo    | 3mo    | 6mo    | 1yr    | 2yr    | 3yr    | 4yr    |
|----------------|--------|--------|--------|--------|--------|--------|--------|--------|
| Blended ARPPU  | $X     | $X     | $X     | $X     | $X     | $X     | $X     | $X     |
| ARPU (net)     | $X     | $X     | $X     | $X     | $X     | $X     | $X     | $X     |
| Revenue        | $X     | $X     | $X     | $X     | $X     | $X     | $X     | $X     |
| ROAS           | X.Xx   | X.Xx   | X.Xx   | X.Xx   | X.Xx   | X.Xx   | X.Xx   | X.Xx   |
```

Below the table, state:
- **Breakeven point**: the first time horizon where ROAS ≥ 1.0 (or "Not reached within 4 years" if never)
- **CAC**: $X
- **LTV:CAC ratio** (at 4 years): X:1

### Step 4: Performance Assessment

Compare the user's metrics against these thresholds and give a verdict for each:

**CR to Purchase (effective, including trial funnel if applicable):**
- ≥ 8%: Excellent
- 5–8%: Good (above average)
- 3–5%: Average
- < 3%: Below average — paywall needs work

**Trial Start Rate (if user has trials):**
- ≥ 14%: Excellent (North America benchmark)
- 10–14%: Good
- 7–10%: Average (global range)
- < 7%: Below average — paywall isn't compelling enough to start a trial

**Trial-to-Paid Rate (if user has trials):**
- ≥ 40%: Excellent — trial experience converts well
- 28–40%: Good (above global average of 27.8%)
- 15–28%: Average — trial may be too long or value isn't clear enough
- < 15%: Poor — users aren't finding value during the trial period

**ROAS Breakeven Timeline:**
- < 3 months: Exceptional — scale aggressively
- 3–6 months: Excellent — healthy economics
- 6–12 months: Normal — sustainable but watch retention
- 12–24 months: Warning — long payback, risky with paid acquisition
- > 24 months or never: Critical — unit economics are broken

**LTV:CAC Ratio (at 4 years):**
- > 5:1: Strong — you may be underinvesting in growth
- 3:1–5:1: Healthy — sustainable unit economics
- 2:1–3:1: Marginal — optimize before scaling
- < 2:1: Unprofitable — fix pricing, retention, or CPI before spending on ads

**ARPU vs Category Benchmarks:**
Compare the user's 12-month ARPU against the LTV Benchmarks table earlier in this skill. State whether they're above, at, or below the category median.

### Step 5: Expert Advice Engine

Based on the calculated results, provide the most relevant advice from this decision tree. Pick the 2–3 most impactful issues — don't overwhelm.

**If blended ARPPU is below category median:**
> "Your blended ARPPU of $X is below the category median of $Y. Higher-priced apps earn 3x the LTV. Consider raising your [highest-distribution plan] price by 20–30%."

**If CAC exceeds 12-month ARPU (ROAS < 1.0 at 1yr):**
> "Your CAC of $X means you need $X in net LTV to break even, but your 1-year ARPU is only $Y. Either reduce CPI (target < $Z), improve CR to purchase, or raise prices."

**If plan mix is suboptimal (annual distribution < 20% and annual LTV is 2x+ monthly):**
> "Only X% of users choose your annual plan, but it delivers Nx the LTV of monthly. Restructure your paywall to emphasize the annual plan — make it the default/highlighted option with a visible savings callout."

**If breakeven > 12 months:**
> "ROAS hits 1.0 only at [T]. Consider adding a weekly plan with a free trial to front-load revenue — weekly plans capture 47% of subscription revenue industry-wide and break even much faster."

**If retention is below benchmarks (when user provides custom retention data):**
> "Your [plan] retention drops to X renewals at 1yr vs the benchmark of Y. Focus on onboarding improvements and week 2–4 engagement hooks — faster time-to-value is the #1 retention lever."

**If LTV:CAC > 5:1:**
> "Your LTV:CAC ratio of X:1 is very strong — you're likely underinvesting in growth. You could increase CPI by up to $X and still maintain healthy 3:1 economics."

**If Trial Start Rate < 10% (when user has trials):**
> "Only X% of installs start a trial — the global average is 11.2% and North America hits 14.5%. Your paywall isn't compelling enough. Test: stronger benefit copy, video backgrounds (8–15% conversion lift), or a hard paywall that forces the trial decision."

**If Trial-to-Paid Rate < 20% (when user has trials):**
> "Your trial-to-paid rate of X% is well below the 27.8% benchmark. Users are starting trials but not converting. Check: is the trial too long (users forget)? Does the user hit the core value proposition within the first 2–3 days? Add engagement nudges during the trial and a compelling pre-expiry notification."

**If user has no trials but could benefit:**
> "You're converting directly without trials. For your plan type, trials boost LTV by up to 64% and improve day-30 retention from 23% → 42% on weekly plans. Consider adding a 3-day or 7-day free trial."

**If Apple fee is 30% and revenue < $1M:**
> "You're paying the 30% commission but may qualify for Apple's Small Business Program (15% rate) if your revenue is under $1M. Switching would increase your net ARPU by ~17.6%."

### Step 6: Scenario Modeling

After presenting the analysis, proactively offer 2–3 "what-if" scenarios tailored to the user's weakest metrics. Recalculate the full table for each scenario.

Example scenarios to offer:
- "What if you raise your [most popular plan] price by 30%?"
- "What if CR improves from X% to Y%?" (suggest a realistic target)
- "What if you add a weekly plan at $X/week?"
- "What if you shift plan distribution to 50% annual?"
- "What if you reduce CPI from $X to $Y through better targeting?"
- "What if you qualify for Apple's 15% Small Business rate?"
- "What if you add a free trial and hit the 11.2% trial start / 27.8% trial-to-paid benchmarks?"
- "What if you improve trial-to-paid from X% to Y% with better onboarding?"

Let the user pick which scenarios to explore, then recalculate and compare side-by-side with the baseline.

### Example Walkthrough

**User input:** "I have an app with annual ($89.99, 25%), quarterly ($39.99, 35%), monthly ($19.99, 35%), lifetime ($99, 5%). My CR is 6%, CPI is $3, 40k installs/month."

**Step-by-step calculation (using default retention benchmarks, 15% Apple fee):**

Per-plan LTV at each horizon:

| Plan | Dist | Price | 7d | 1mo | 3mo | 6mo | 1yr | 2yr | 3yr | 4yr |
|------|------|-------|-----|------|------|------|------|------|------|------|
| Annual | 25% | $89.99 | $89.99 | $89.99 | $89.99 | $89.99 | $98.99 | $101.69 | $103.49 | $104.39 |
| Quarterly | 35% | $39.99 | $39.99 | $39.99 | $45.99 | $47.99 | $53.99 | $55.99 | $57.99 | $56.79 |
| Monthly | 35% | $19.99 | $19.99 | $27.99 | $33.98 | $39.98 | $51.97 | $59.97 | $63.97 | $65.97 |
| Lifetime | 5% | $99.00 | $99.00 | $99.00 | $99.00 | $99.00 | $99.00 | $99.00 | $99.00 | $99.00 |

Blended ARPPU = Σ(LTV × distribution):
- 7d: (89.99×0.25) + (39.99×0.35) + (19.99×0.35) + (99×0.05) = 22.50 + 14.00 + 7.00 + 4.95 = **$48.44**
- 1mo: (89.99×0.25) + (39.99×0.35) + (27.99×0.35) + (99×0.05) = 22.50 + 14.00 + 9.80 + 4.95 = **$51.24**
- 3mo: (89.99×0.25) + (45.99×0.35) + (33.98×0.35) + (99×0.05) = 22.50 + 16.10 + 11.89 + 4.95 = **$55.44**
- 6mo: (89.99×0.25) + (47.99×0.35) + (39.98×0.35) + (99×0.05) = 22.50 + 16.80 + 13.99 + 4.95 = **$58.24**
- 1yr: (98.99×0.25) + (53.99×0.35) + (51.97×0.35) + (99×0.05) = 24.75 + 18.90 + 18.19 + 4.95 = **$66.79**
- 2yr: (101.69×0.25) + (55.99×0.35) + (59.97×0.35) + (99×0.05) = 25.42 + 19.60 + 20.99 + 4.95 = **$70.96**
- 3yr: (103.49×0.25) + (57.99×0.35) + (63.97×0.35) + (99×0.05) = 25.87 + 20.30 + 22.39 + 4.95 = **$73.51**
- 4yr: (104.39×0.25) + (56.79×0.35) + (65.97×0.35) + (99×0.05) = 26.10 + 19.88 + 23.09 + 4.95 = **$74.01**

ARPU (net) = Blended ARPPU × 0.85:
- 7d: **$41.18** | 1mo: **$43.56** | 3mo: **$47.12** | 6mo: **$49.50** | 1yr: **$56.77** | 2yr: **$60.32** | 3yr: **$62.49** | 4yr: **$62.91**

Revenue = 40,000 × 0.06 × ARPU:
- 7d: **$98,826** | 1mo: **$104,534** | 3mo: **$113,098** | 6mo: **$118,808** | 1yr: **$136,248** | 2yr: **$144,763** | 3yr: **$149,966** | 4yr: **$150,990**

ROAS = (0.06 × ARPU) / $3:
- 7d: **0.82** | 1mo: **0.87** | 3mo: **0.94** | 6mo: **0.99** | 1yr: **1.14** | 2yr: **1.21** | 3yr: **1.25** | 4yr: **1.26**

**Summary:**
- CAC = $3 / 0.06 = **$50.00**
- Breakeven: ~6 months (ROAS approaches 1.0)
- LTV:CAC = $62.91 / $50.00 = **1.26:1**

**Assessment:** LTV:CAC of 1.26:1 is below the healthy 3:1 threshold. While ROAS breaks even around 6 months (normal), the long-term unit economics are marginal. Advice: raise prices (especially quarterly/monthly), shift plan mix toward annual, and reduce CPI if possible.

