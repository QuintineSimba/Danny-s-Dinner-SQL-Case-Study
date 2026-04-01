# Danny's-Dinner-SQL-Case-Study
---
Full SQL solutions for all 10 questions are available in the [`Danny's Diner.sql`](./Danny'sDiner.sql) file in this repository.
## Objective

Danny's Diner is evaluating whether to expand its customer loyalty programme. To support that decision, this analysis examines three months of transactional data to uncover customer spending patterns, visit frequency, and menu preferences.

The goal is to give Danny a clear, data-backed picture of how his customers behave — who his most valuable customers are, what they buy, and how the loyalty programme is influencing purchasing decisions — so he can make an informed call on the programme's future.

---

## Methodology

**Tools used:** MySQL 8.0

**Dataset:** Three relational tables provided by Danny Ma as part of the [8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-1/)

| Table | Description |
|---|---|
| `sales` | Customer-level transaction records — customer ID, order date, product ID |
| `menu` | Product lookup table — product ID, name, and price |
| `members` | Loyalty program data — customer ID and membership join date |

---

## Key Findings

---

### 1. Revenue is concentrated in one customer
*Concept used: `JOIN` + `GROUP BY` + `SUM` — the sales table holds transactions but not prices, so joining to the menu table was necessary to calculate what each customer actually spent.*

**Q1 — Total amount spent per customer**

| customer_id | total_spent |
|---|---|
| A | $76 |
| B | $74 |
| C | $36 |

Customer A is the highest spender, followed closely by B. Customer C spends less than half of either member. For a small restaurant, this level of revenue concentration is a risk — if Customer A stops coming, Danny feels it immediately. The loyalty programme is the right lever to lock in that relationship before it becomes a problem.

---

### 2. Visit frequency does not equal spend
*Concept used: `COUNT(DISTINCT)` — customers can visit multiple times in a day, so counting distinct order dates rather than raw transactions gives an accurate picture of actual visit frequency.*

**Q2 — Number of days each customer visited**

| customer_id | days_visited |
|---|---|
| A | 4 |
| B | 6 |
| C | 2 |

Customer B visits most frequently but spends less than Customer A. This tells Danny that B is a habitual visitor who spends modestly per visit, while A visits less often but spends more each time. These are two different customer profiles that warrant different retention strategies — frequency rewards for B, high-value perks for A.

---

### 3. First impressions reveal menu entry points
*Concept used: Window function `RANK() OVER (PARTITION BY)` inside a subquery — ranking purchases per customer by date allowed the first transaction to be isolated without removing other data from the dataset.*

**Q3 — First item purchased by each customer**

| customer_id | order_date | product_name |
|---|---|---|
| A | 2021-01-01 | sushi |
| A | 2021-01-01 | curry |
| B | 2021-01-01 | curry |
| C | 2021-01-01 | ramen |

Every customer's first visit was on the same date. Curry was the entry point for both A and B, while C came in through ramen. Understanding what brings customers through the door for the first time informs how Danny should position his menu to new visitors — curry appears to be the strongest hook.

---

### 4. Ramen is carrying the menu
*Concept used: `ORDER BY` + `LIMIT` — after counting purchases per item, ordering by frequency descending and limiting to one row surfaces the single most purchased product cleanly.*

**Q4 — Most purchased item overall**

| product_name | times_purchased |
|---|---|
| ramen | 8 |

Ramen was ordered 8 times — more than sushi and curry combined. This has direct implications for inventory planning, supplier relationships, and menu positioning. Ramen is the anchor product and should be protected, not rotated out or discounted casually. Any supply disruption to ramen is a direct revenue risk.

---

### 5. Each customer has a distinct favourite
*Concept used: Subquery with `RANK()` — purchases were first counted per customer per item using `GROUP BY`, then ranked within each customer partition to identify their most purchased item.*

**Q5 — Most popular item per customer**

| customer_id | product_name | times_purchased |
|---|---|---|
| A | ramen | 3 |
| B | curry | 2 |
| B | sushi | 2 |
| B | ramen | 2 |
| C | ramen | 3 |

Customer A and C are ramen loyalists. Customer B spreads purchases evenly across all three items — the most diverse buyer on the menu. This matters for personalisation: a targeted ramen promotion would resonate with A and C, while B responds to variety and would benefit from a multi-item reward structure.

---

### 6. The loyalty programme changes purchasing behaviour
*Concept used: Three-table `JOIN` with date filter — joining sales, menu, and members together, then filtering where order date falls on or after the membership join date, isolated only post-membership transactions.*

**Q6 — First item purchased after becoming a member**

| customer_id | product_name |
|---|---|
| A | curry |
| B | sushi |

**Q7 — Last item purchased before becoming a member**

| customer_id | product_name |
|---|---|
| A | sushi |
| A | curry |
| B | sushi |

Customer A's last pre-membership purchase included sushi — a higher points item — suggesting some awareness of the programme before joining. Post-membership, A shifted to curry. Customer B purchased sushi both before and after joining, indicating the points multiplier may be reinforcing an existing preference. This is early evidence that the programme is influencing what members order.

---

### 7. Members spent meaningfully before joining
*Concept used: `COUNT` + `SUM` with date filter — filtering transactions to only those before the membership join date and aggregating per customer revealed pre-membership spending behaviour.*

**Q8 — Total items and amount spent before becoming a member**

| customer_id | total_items | total_spent |
|---|---|---|
| A | 2 | $25 |
| B | 3 | $40 |

Customer B spent more and bought more items before joining the programme than Customer A. This suggests B was already an engaged customer before the loyalty incentive existed — the programme captured a customer who was already valuable, rather than creating value from scratch. Danny should use this same approach to recruit Customer C.

---

### 8. Points totals reveal loyalty programme ROI
*Concept used: `CASE WHEN` inside `SUM` — a conditional multiplier was applied per item (2x for sushi, 1x for all others) before aggregating, allowing the points calculation to reflect the programme's tiered reward structure.*

**Q9 — Total points per customer (sushi 2x multiplier)**

| customer_id | total_points |
|---|---|
| A | 860 |
| B | 940 |
| C | 360 |

Despite spending less overall, Customer B edges out A on points due to more diverse purchasing including sushi. Customer C — with no membership — sits at 360 points they will never redeem. This is value Danny's programme is generating but not capturing. Enrolling Customer C converts dormant spend into active loyalty.

---

### 9. The first-week bonus is the programme's strongest hook
*Concept used: `CASE WHEN` with `BETWEEN` and `INTERVAL` — the first-week bonus required a date range condition layered on top of the existing points logic, checking whether each transaction fell within 7 days of the customer's join date before applying the multiplier.*

**Q10 — Total points for members at end of January (first-week 2x bonus included)**

| customer_id | total_points |
|---|---|
| A | 1370 |
| B | 820 |

With the first-week bonus applied, Customer A jumps significantly ahead of B — from below B in Q9 to nearly double in Q10. This tells Danny the first-week mechanic disproportionately rewards customers who spend heavily right after joining. It is the programme's most commercially valuable feature and should be communicated prominently at the point of enrolment to drive immediate post-signup transactions.

---

## Summary

Danny's Diner has a small but behaviourally distinct customer base. The data reveals a clear revenue concentration risk, an untapped loyalty opportunity in Customer C, and a menu anchored by ramen that the business should double down on rather than diversify away from.

The loyalty programme has commercial merit — particularly its first-week bonus mechanic — but its impact is limited by low enrolment. Expanding the programme and actively recruiting Customer C is the single highest-leverage action Danny can take based on the available data.



---

*Dataset and case study credit: [Danny Ma — 8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-1/)*
