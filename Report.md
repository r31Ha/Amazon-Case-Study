# Understanding Product Success in the Amazon Marketplace

**A data-driven analysis of pricing, promotion, and demand across 42,675 Amazon product listings**

---

## Business Objective

Identify the factors associated with product demand and marketplace success, and understand how pricing, promotions, customer feedback, and sponsored placement interact within an Amazon-style e-commerce marketplace.

**A note on scope:** This dataset is **product-level observational data**, not transaction-level customer data. It contains no individual customers, orders, basket contents, conversion rates, impressions, traffic, advertising spend, seller information, margins, or returns. Findings should therefore be read as *"products with characteristic X are associated with higher observed purchase volume,"* not *"customers prefer X."* This distinction is maintained throughout the report.

---

## 1. Dataset Overview

- **42,675 product listings**, scraped from Amazon, covering pricing, ratings, reviews, recent purchase signals, discounts, availability, and delivery details
- Every field arrived as raw text and required cleaning: ratings embedded in sentences ("4.6 out of 5 stars"), prices with currency symbols and thousands separators, purchase counts as display buckets ("300+ bought in past month"), and badges mixing several distinct signals in one column
- No ground-truth product category existed in the raw data; categories were inferred from product titles using keyword matching. This method is imprecise — roughly 55% of products fell into a residual "Other" bucket — and category-level findings should be read as directional, not exact

### Key engineered fields

| Field | Description |
|---|---|
| `rating_clean` | Numeric star rating (0–5) |
| `reviews_clean` | Total review count |
| `bought_last_month_clean` | Recent purchase volume — **a bucketed floor value** (e.g., "300+" → 300), not an exact count |
| `current_price_clean`, `listed_price_clean`, `discount_pct` | Cleaned pricing and computed discount percentage |
| `badge_best_seller`, `badge_amazons_choice`, `badge_limited_deal` | Split from a single overloaded badge column |
| `has_coupon`, `coupon_pct`, `coupon_dollar` | Parsed coupon type and value |
| `is_sponsored` | Sponsored vs. organic listing |
| `category_inferred` | Keyword-based category assignment |

**Important limitation carried through every analysis below:** `bought_last_month_clean` is a *display bucket*, not an exact figure. Directional patterns (X sells more than Y) are reliable; precise multipliers ("10x more") are approximate.

---

## 2. Demand Is Extremely Concentrated (The Long Tail)

The clearest and most consequential finding in this dataset: **a small fraction of products account for nearly all observed demand.**

| Top % of products (by purchases) | Share of total purchases |
|---|---|
| Top 1% | 36.2% |
| Top 5% | 77.4% |
| Top 10% | 84.1% |
| Top 25% | 91.6% |
| Top 50% | 96.5% |

The bottom half of the catalog — over 16,000 products — contributes just **3.5%** of total observed purchases.

**Interpretation:** The marketplace functions less like a level playing field and more like a winner-take-most competition, where visibility and social proof compound disproportionately for a small number of products. This finding reframes every subsequent question in the report: the goal isn't just "what correlates with more sales," but "what separates the small set of winners from the long tail."

---

## 3. Reviews and Recent Demand Are Related, But Loosely

Comparing review count against recent purchase volume (log-log scale) shows a **moderate positive relationship (r = 0.483)**.

**Interpretation:** More-reviewed products tend to sell more, consistent with social proof driving conversion. But the relationship is loose, not deterministic — products with similar review counts can have very different current demand. Review volume is *one* input into marketplace success, not the whole explanation. This motivated the deeper look at pricing, promotion, and sponsorship below.

---

## 4. Price and Demand — Category Matters

Comparing raw price to demand market-wide would conflate a $10 phone accessory with a $1,000 laptop, so price was analyzed **within each category**, split into quartiles.

**Clear inverse relationship (cheaper sells more):**
- **Other (general merchandise):** cheapest quartile median demand = 500, most expensive = 50 (10x difference)
- **Electronics:** 400 vs. 50 (8x)
- **Home & Kitchen:** 300 vs. 100 (3x)

**Weaker or non-monotonic relationship:**
- **Toys & Games:** demand peaked in the *middle* price tier (600), not the cheapest — possibly reflecting that very cheap toys are lower-quality impulse items outcompeted by solidly-priced mid-tier products
- **Pet Supplies:** generally inverse but noisy, likely due to smaller category sample size

**Interpretation:** Price sensitivity drives purchase volume within most categories, but the relationship is category-specific, not a universal market rule. Category context changes how price should be read as a demand signal.

---

## 5. Discount Percentage Shows No Consistent Relationship With Demand

Unlike price level, discount tier (0%, 1–10%, 10–20%, 20–30%, 30%+) showed **no consistent pattern** across categories:

- **Home & Kitchen** showed a clear climb — demand roughly 10x higher at 20%+ discount than at 0%
- **Pet Supplies** showed a similar pattern with an especially large jump at 30%+ (though a small sample makes this figure sensitive to a few high-performing products)
- **Electronics, Books, Toys & Games** showed flat or inconsistent patterns — discount tier did not clearly predict demand

**A critical caveat — correlation is not causation:** Even where discount level and demand move together, this dataset cannot establish that discounts *cause* higher sales. An equally plausible explanation is reverse causation: sellers may discount products that are *already* popular as part of a promotional strategy, or apply steep discounts to move slow inventory. Without controlled experiments or time-series data, only association can be claimed here — a point worth foregrounding in any business recommendation drawn from this finding.

---

## 6. Sponsored vs. Organic Products — Advertising as a Substitute for Traction, Not Just an Amplifier

| Metric | Organic | Sponsored |
|---|---|---|
| Median price | $79.99 | $69.95 |
| Median rating | 4.5 | 4.5 |
| Median reviews | 383 | **180** |
| Median recent purchases | 200 | **1,000** |
| Best Seller rate | 0.62% | 0.78% |

Sponsored products have **fewer than half the reviews** of organic products — indicating weaker organic social proof — yet show **5x higher median recent purchase volume**, with identical median ratings.

**Interpretation:** This nuances the common assumption that advertising simply amplifies already-successful products. Instead, sponsorship appears to function as an *alternative entry point* into the demand cycle: sellers use paid visibility to generate purchase volume for products that haven't yet accumulated organic reviews, rather than reserving ad spend for proven winners. One honest limitation: newness is a plausible lurking variable here — sponsored products may simply be newer listings still in the process of accumulating reviews, and this dataset cannot fully separate that effect from the sponsorship effect itself.

---

## 7. Best Seller Products — A Small, Self-Reinforcing Elite

Only **275 of 42,675 products (0.6%)** hold Best Seller status. Comparing them to the rest of the catalog:

| Metric | Not Best Seller | Best Seller |
|---|---|---|
| Median price | $77.00 | **$39.99** (~48% cheaper) |
| Median discount | 0% | **18.3%** |
| Median rating | 4.5 | 4.6 |
| Median reviews | 340 | **8,449** (~25x more) |
| Median recent purchases | 200 | **6,000** (~30x more) |
| Sponsored rate | 16.4% | 20.0% |

**Interpretation:** Best Sellers are cheaper, more heavily discounted, and carry dramatically more reviews and purchase volume than the rest of the catalog. This is consistent with the "marketplace flywheel": competitive pricing and discounting likely help products accumulate the review volume and purchase momentum needed to earn Best Seller status, which in turn reinforces further sales through social proof and increased visibility. The badge marks a tiny, elite, and largely self-reinforcing tier of the marketplace — echoing the long-tail concentration finding in Section 2.

---

## 8. Product Segmentation — Category Dynamics Differ Beneath the Aggregate Pattern

Products were segmented into four groups using median review count and median purchase volume as thresholds:

| Segment | Definition | Share of catalog |
|---|---|---|
| Established winners | High reviews, high recent purchases | 37% |
| Weak products | Low reviews, low recent purchases | 32% |
| Emerging winners | Low reviews, high recent purchases | 17% |
| Mature/niche | High reviews, low recent purchases | 13% |

**Category-level standouts:**
- **Toys & Games:** 89% Established winners, only 3% Weak — a category with unusually concentrated, proven demand
- **Sports & Outdoors:** 48% Emerging winners — an unusually high share of products gaining traction before building a large review base, suggesting more room for newer entrants to break through
- **Pet Supplies:** highest Mature/niche share (27%) — consistent with staple, repeat-purchase products (food, litter) that accumulate reviews over years without necessarily spiking in any given month
- **Home & Kitchen** and **Pet Supplies** carry the largest Weak-product shares (47%, 37%) — broad categories with substantial long-tail inventory

**Caveat:** Smaller categories (Beauty n=9, Clothing n=20, Sports & Outdoors n=177) produce noisy percentages — a single product can swing a share by several points, so these specific figures should be treated as suggestive rather than robust.

**Interpretation:** The long-tail pattern from Section 2 isn't uniform — it plays out differently by category. Some categories (Toys & Games) reward established trust and proven track records; others (Sports & Outdoors) show more openness to newer products breaking through quickly.

---

## 9. Predicting Best Seller Status

A classification model was built to predict `is_best_seller` from product features, explicitly designed around a severe class imbalance: only 0.6% of products are Best Sellers, so an accuracy-based evaluation would be misleading — a model that always predicts "not Best Seller" would score 99.4% accuracy while being useless. Precision, recall, and F1 for the minority class were used instead.

### Model comparison

| Metric | Logistic Regression | Random Forest |
|---|---|---|
| Best Seller recall | 0.69 (31/45 caught) | 0.58 (26/45 caught) |
| Best Seller precision | 0.03 (928 false positives) | **0.12** (186 false positives) |

Random Forest offered a substantially more usable precision/recall trade-off — 5x fewer false positives for a modest recall cost. Which model is "better" depends on the business use case: a "flag for review" tool tolerates false positives and would favor the higher-recall logistic regression; a tool meant to produce a trustworthy shortlist would favor Random Forest's higher precision.

### What predicts Best Seller status

| Feature | Importance |
|---|---|
| Review count | 44.2% |
| Discount % | 21.7% |
| Price | 15.8% |
| Rating | 9.6% |
| Sponsored status | 3.1% |
| Sustainability badge | 2.7% |
| Buy Box availability | 2.5% |
| Coupon presence | 0.4% |

**Interpretation:** This confirms and quantifies the descriptive findings from Section 7 — review volume is by far the strongest predictor of Best Seller status, followed by discount and price. Notably, the marketing-lever variables (sponsorship, coupons, sustainability badges) contribute almost nothing to prediction. This is a genuinely non-obvious finding: **sponsorship drives purchase volume (Section 6), but it does not predict Best Seller status.** These are two different outcomes with different drivers — paid visibility can move units without building the organic trust signal (reviews) that the Best Seller badge appears to reward.

---

## 10. Summary of Findings

1. **Demand is extremely concentrated.** The top 5% of products account for over three-quarters of observed purchases; the bottom half of the catalog barely registers.
2. **Reviews correlate with demand, moderately.** More reviews associate with more purchases, but the relationship is loose — other factors matter too.
3. **Price matters, but only within category.** Cheaper products generally sell more, though the effect size and even direction vary by category.
4. **Discounts show no reliable demand relationship**, and any apparent link is confounded by the likelihood that sellers discount products for reasons unrelated to pure price sensitivity (already-popular items being promoted further, or slow inventory being cleared).
5. **Sponsorship looks like a substitute for organic traction, not just an amplifier of it** — sponsored products have fewer reviews but higher recent purchase volume than organic ones.
6. **Best Sellers are a small, self-reinforcing elite** — cheaper, more discounted, and dramatically better-reviewed than the rest of the catalog.
7. **Category context changes the story** — segmentation shows very different demand dynamics between categories like Toys & Games (winner-dominated) and Sports & Outdoors (more open to newcomers).
8. **Review volume is the single strongest predictor of Best Seller status** (44% of model importance) — far ahead of marketing-lever variables like sponsorship or coupons, which barely matter for this particular outcome.

---

## 11. Limitations

- **Bucketed purchase data:** `bought_in_last_month` values are floor estimates from Amazon's own display buckets, not exact sales figures. Directional findings are reliable; precise multipliers are approximate.
- **Inferred categories:** No ground-truth category field existed; keyword-based inference is imprecise, and roughly 55% of products fell into a residual "Other" category.
- **No causal claims:** This is observational, product-level, cross-sectional data. Relationships between price, discounts, sponsorship, and demand describe association, not causation. In particular, the discount-demand and sponsorship-demand findings are consistent with reverse causation (successful products being promoted further) as much as with promotion causing success.
- **No customer-level data:** Individual buyer behavior, conversion rates, traffic, and ad spend are not observable in this dataset, so conclusions are framed at the product level throughout.

---

## 12. Methodology Note

Analysis performed in Python (pandas, matplotlib, scikit-learn). Data cleaning addressed inconsistent text formats across all fields (embedded units, currency symbols, thousands separators, overloaded badge columns). Category-controlled comparisons used quartile binning within category to avoid confounding price effects with product type. The predictive model used a stratified train/test split, `class_weight='balanced'` to address the 0.6% positive class rate, and was evaluated on precision/recall/F1 rather than accuracy given the severe imbalance.
