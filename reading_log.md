# Reading Log — Day 8

Re-read notebooks 01–07 as if seeing them for the first time.

- **What still confuses me:** the gap between how well `complaints_count` and `tenure_months` separate churn in the plain group-by comparisons (notebook 03) versus how little of that shows up in the logistic regression's coefficients — I'd want to dig into feature scaling and correlation between features to understand why.
- **What I'd explain better:** the jump from raw churn EDA (01–03) to the modeling notebooks (05–06) skips over *why* certain buckets/thresholds were chosen (e.g. the tenure and complaint bins) — those choices deserve a sentence of justification in the notebook itself, not just in my head.
- **What I'm proud of:** catching and fixing real bugs while cleaning up for this handoff — a hardcoded local file path that would have broken the notebook for anyone else, a stray character that caused a cell to fail, and a computed-but-never-saved feature — the kind of thing that's easy to miss when you're heads-down building.
