# BankDataAnalysis
Power BI branch performance ranking built on a synthetic banking dataset. Modeled a proper star schema (Fact/Dim tables), then built a composite score combining loan volume, default rate, and interest income — standardized with Z-scores to fairly compare metrics on different scales, with transparent, documented weighting.


Bank Branch Performance Analysis — Power BI

A composite branch performance ranking built on a synthetic 136-row banking dataset, modeled as a proper star schema and scored using statistical standardization (Z-scores) to fairly combine metrics on different scales.

What I Did

Data Modeling

Rebuilt the flat source data into a star schema: Fact_Bank_Data at the center, with Dim_Customer, Dim_Branch, Dim_Region, Dim_Account_Type, and Dim_Loan_Status as connected dimension tables.
Applied a consistent rule for separating fact from dimension data: does a value describe the entity (customer, branch) or the event (a specific loan)? Balance, duration, and loan status stayed in the fact table; customer age and account type were moved to their own dimensions.
Verified all relationships for correct cardinality (one-to-many) and active status before building anything on top of the model.

Data Cleaning

Used Power Query's Column Quality, Column Distribution, and Column Profile tools to systematically identify issues rather than manual inspection — caught live duplicate IDs, whitespace-based branch name duplicates, and inconsistent category casing (Savings/savings/SAVINGS) this way.
Resolved multiple inconsistent date formats using a conditional Custom Column.
Standardized inconsistent Loan_Status values (Y/N/Yes/No/Active/Closed/Defaulted) after confirming their intended meaning rather than assuming.

Composite Performance Score

Built three branch-level metrics: Loan Volume (Active + Defaulted loan value), Default Rate (defaults as a share of all loans ever issued), and Interest Income (active loans only, since the request was about current performance).
Standardized each metric using Z-scores (value − mean) / standard deviation rather than Min-Max normalization, specifically because the dataset contains genuine outliers (one branch's loan volume is roughly 30x the smallest) that would have compressed most branches into a meaningless range under Min-Max.
Flipped the Default Rate Z-score (multiplied by −1) so that "low risk" consistently contributes positively to the score, matching the direction of the other two metrics.
Combined the three standardized scores into one Performance Score using equal weighting (0.33 each) as a transparent, documented starting point — not a claimed final answer. Relative weighting of volume vs. risk vs. income is a business strategy decision, not one a junior analyst should make unilaterally; equal weighting was chosen specifically because it makes no unstated assumption about priority.

Key Finding

Accra Central ranks first by a wide margin (Performance Score of 2.09, more than 50% higher than the second-place branch), driven primarily by loan volume and interest income rather than a low default rate. Three branches (Wa, Bolgatanga, Nkawkaw) score below zero once all three metrics are combined — despite Nkawkaw having a very small loan book, its 100% historical default rate on its only recorded loan pulls its composite score down significantly.

Dashboard Pages

Loan Status by Branch — stacked bar showing Active vs. Defaulted loan value per branch.
Active Loans & Account Holders — combo chart comparing active loan value against customer count per branch.
Composite Ranking — single sorted bar chart of the final Performance Score by branch.
Component Breakdown — the three individual Z-scores shown side by side, so the ranking on page 3 can be audited rather than taken on faith.
Raw Metrics Table — the underlying dollar figures and rates before standardization, for full transparency.
Tools

Power BI Desktop · Power Query · DAX (CALCULATE, SUMX, FILTER, SUMMARIZE, ADDCOLUMNS, AVERAGEX, STDEVX.P, DIVIDE, VAR/RETURN)

Known Issue

The Default Rate column shows blank rather than 0 for several branches with zero recorded defaults. This was investigated at length — confirmed the DIVIDE function includes a zero-fallback argument, confirmed the underlying Branch_id and Loan_Status_id fields carry consistent data types, and confirmed no duplicate or stale fields were being referenced in the affected visuals — without identifying the root cause. Flagged here rather than hidden, for follow-up.

What I'd Improve
Resolve the Default Rate blank issue above.
Replace the equal-weighting placeholder with actual business-prioritized weights once available.
Add a written recommendation per branch, not just a ranking, tying the specific Z-score components back to a concrete action (e.g., a branch scoring low on Default Rate specifically warrants risk review, independent of its overall rank).


## Dashboard Pages

### Composite Ranking
![Composite Ranking chart showing branches ranked by performance score](Bank_analysis_page_3.png)

### Composite Ranking
![Composite Ranking chart showing branches ranked by performance score](Bank_analysis_page_2.png)

### Composite Ranking
![Composite Ranking chart showing branches ranked by performance score](Bank_analysis_page_1.png)

### Component Breakdown
![Component Breakdown](CarAnalysis.png)
