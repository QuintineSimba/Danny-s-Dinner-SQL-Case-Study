# Danny's-Dinner-SQL-Case-Study
---

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

**SQL concepts applied across the 10 questions and 2 bonus questions:**

- `JOIN` — linking sales to menu and members across multiple queries
- `GROUP BY` + aggregate functions (`SUM`, `COUNT`) — spending and frequency analysis
- `COUNT(DISTINCT)` — deduplicating visit days per customer
- Window functions (`RANK() OVER PARTITION BY`) — identifying first purchases and most popular items per customer
- Subqueries — filtering ranked results
- `CASE WHEN` — building conditional point multiplier logic
- Date filtering with `BETWEEN` and `INTERVAL` — isolating pre/post membership purchases and the loyalty programme's first-week bonus window

---

## Key Findings

**1. Revenue is concentrated in one customer**
Customer A is the highest spender across all transactions. For a small restaurant still finding its footing, over-reliance on a single customer is a revenue risk — retaining Customer A should be a priority, and the loyalty programme is the right lever to do that.

**2. Ramen is carrying the menu**
Ramen is the most purchased item across all customers by a significant margin. This has direct implications for inventory planning, supplier relationships, and menu positioning — ramen should be protected as the anchor product, not rotated out or discounted casually.

**3. Customer B is the most engaged member**
Customer B visits consistently, purchases across multiple menu items, and is a loyalty programme member. This is the profile of a customer who responds to incentives — targeted rewards or early access offers would likely increase their spend further.

**4. Customer C is a loyalty programme blind spot**
Customer C visits regularly and has a clear preference for ramen, but has never joined the loyalty programme. Every purchase they make earns them nothing and costs the restaurant a retention opportunity. A simple enrolment nudge could convert them into a trackable, incentivised customer.

**5. The loyalty programme's first-week bonus is its strongest hook**
The 2x points window in the first 7 days after joining creates a strong incentive to spend immediately after enrolment. This is the programme's most commercially valuable mechanic — it drives early post-signup transactions and builds purchase habits before customers disengage.

**6. Sushi is underperforming relative to its point value**
Despite carrying the highest points multiplier, sushi is the least purchased item. This suggests customers are either price-sensitive to its $10 price point relative to ramen and curry, or unaware of the points benefit. A targeted promotion communicating the sushi multiplier could shift purchasing behaviour and increase programme engagement.

---

## Summary

Danny's Diner has a small but behaviourally distinct customer base. The data reveals a clear revenue concentration risk, an untapped loyalty opportunity in Customer C, and a menu anchored by ramen that the business should double down on rather than diversify away from.

The loyalty programme has commercial merit — particularly its first-week bonus mechanic — but its impact is limited by low enrolment. Expanding the programme and actively recruiting Customer C is the single highest-leverage action Danny can take based on the available data.

Full SQL solutions for all 10 questions are available in the [`Danny's Diner.sql`](./Danny'sDiner.sql) file in this repository.

---

*Dataset and case study credit: [Danny Ma — 8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-1/))*
