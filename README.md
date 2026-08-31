# State Data Center Legislation and Facebook Discussion of Data Center Policy, 2023–2026

## Central Research Question

How have U.S. states approached data-center development since 2023, and how did Facebook attention and policy framing in posts returned by the `datacenter` query relate to legislative activity from 2024–2026?

This observational QSS20 project uses pandas, functions, merges and merge diagnostics, regular expressions, text preprocessing, count/TF-IDF representations, LDA topic modeling, scikit-learn logistic regression, cross-validation, and reproducible visualization. It does not claim that Facebook discussion causes legislation, legislation causes engagement, or engagement measures public opinion.

## Facebook Corpus and Analytical Scope

The final project uses only the unspaced **`datacenter`** Facebook Content Library export. It contains 66,055 raw rows from January 1, 2024 through August 17, 2026.

After filtering:

- 24,947 rows are English.
- 24,915 English posts contain the expected datacenter spelling.
- 3,016 posts match the transparent policy dictionary.
- 19,852 normalized-text-unique relevant posts remain after text deduplication.
- The April 1–August 17, 2026 discourse subset contains 10,597 relevant posts, 1,995 policy posts, and 1,509 normalized-text-unique policy posts.

The same query definition supports the longitudinal, frame, topic, engagement, state-linkage, and prediction analyses. This avoids a 2026 structural break and makes the project easier to interpret. It also narrows the claim: the corpus misses posts using only the spaced spelling `data center`, so it is not a census of Facebook data-center discussion.

The supplied spaced-query export is retained locally but unused. The previous two-query analysis is preserved under `archive/two_query_version/`.

## Principal Findings

### Legislative Activity, Policy Orientation, and Advancement

- The current bill file contains 411 unique relevant bills across fifteen states; 395 have completed 1–10 policy-orientation scores and form the primary scored analysis.
- Virginia is most active with 116 scored bills, followed by Illinois (53), New York (46), and Minnesota (44).
- Observed advancement is not monotonic across policy-orientation categories: 21.6% for restrictive bills (16/74), 40.7% for neutral or mixed bills (83/204), and 25.6% for supportive bills (30/117).
- The five-fold legislative baseline model improves on majority-class accuracy (0.691 versus 0.673) and records precision 0.518, recall 0.783, F1 0.623, and ROC-AUC 0.765. Predictors include only policy orientation, sponsor counts, originating chamber, introduction year, and region.

### Facebook Attention and Policy Discourse

- Unique normalized texts are relatively stable through 2024 and early 2025, then rise strongly during 2026. They increase from 832 in March 2026 to 1,782 in May and 2,071 in July. Unique owners also rise from 499 to 949 and 1,158 over those months. August 2026 is partial.
- Because the rise appears in unique texts and unique owners—not only raw rows—it is not explained solely by duplicate or syndicated posts. It could still reflect changing use of the unspaced term, changes in the pages represented, or export behavior rather than Facebook discussion generally.
- Among the 1,995 policy posts in the April–August 2026 window, overlapping frames are led by regulation/community control (67.4%), energy/utility costs (56.8%), water/environment (49.9%), AI/technology competition (49.6%), economic development/jobs (41.6%), and taxes/incentives/subsidies (20.4%).
- A six-topic interpretation identifies Claremore transparency/recall activism, local projects and public finance, water/environmental effects, state/local restrictions, AI infrastructure, and peripheral federal/business commentary. Representative posts show that false positives and tangential AI material remain, so manual validation is necessary.
- The page-relative TF-IDF engagement model exhibits limited predictive performance. On a stratified post holdout, accuracy is 0.577, F1 is 0.350, and ROC-AUC is 0.559, compared with majority accuracy 0.569. The owner-group holdout has accuracy equal to the baseline (0.575), F1 0.315, and ROC-AUC 0.548. These metrics indicate that text provides little stable information about above-owner-median engagement in this corpus.

### Temporal Alignment and Exploratory Prediction

- Only 1,805 of 24,915 relevant posts (7.24%) mention at least one study state, producing 2,118 post-state pairs; 156 posts mention multiple study states. A state mention does not identify post origin.
- Event windows include 314 introductions and 57 latest recorded passage events. Median state-matched policy attention is zero in all three pre-event months, one in the introduction month, and two in the second month after introductions. Passage windows have median two posts in the event month. These are descriptive alignments, not causal estimates.
- All state-month Spearman correlations across lags −3 through +3 are small in magnitude, with the largest around 0.12.
- The prespecified social-model gate passes for 268 bills; 64.6% have nonzero prior state-matched attention. On identical folds, the bill-only model has accuracy 0.683, F1 0.564, and ROC-AUC 0.787. Adding strictly pre-introduction Facebook features yields accuracy 0.728, F1 0.588, and ROC-AUC 0.809. This is a modest exploratory predictive improvement, not evidence that attention causes advancement.
- Diagnostics select Virginia, Georgia, and New York for state case studies.

## Analysis Workflow

| Notebook | Purpose | Main inputs | Main outputs |
|---|---|---|---|
| [01 - Collect Open States bills](code/01_collect_openstates_bills.ipynb) | Collect or resume candidate bills with the user's Open States key entered through `getpass` | Open States API | Master candidate file, compressed collection, and progress checkpoint |
| [02 - Classify and score bills](code/02_classify_and_score_bills.ipynb) | Screen every candidate and score relevant bills with the user's Dartmouth Chat key entered through `getpass` | Collected candidate bills | Relevant/scored bills, rejected bills, and resumable classification fields |
| [03 - Visualize the bill landscape](code/03_visualize_bill_landscape.ipynb) | Describe state legislative activity and policy orientation | Scored bills | Event map, state heatmap, and state/quarter tables |
| [04 - Analyze bill advancement](code/04_analyze_bill_advancement.ipynb) | Evaluate observed advancement and the legislative baseline model | Scored bills | Group rates, intervals, metrics, confusion matrix, and leakage audit |
| [05 - Clean Facebook posts](code/05_clean_facebook_posts.ipynb) | Prepare the consistent `datacenter` corpus | One raw Facebook export | Longitudinal and 2026 files, filtering flow, coverage, and duplicate diagnostics |
| [06 - Analyze Facebook discourse](code/06_analyze_facebook_discourse.ipynb) | Analyze attention, frames, topics, and engagement language | Cleaned `datacenter` files | Timeline, frames, topics, representative posts, and model metrics |
| [07 - Compare discourse and legislation](code/07_compare_discourse_and_legislation.ipynb) | Assess temporal and textual alignment and the augmented model | Bills and the `datacenter` corpus | Heatmaps, event windows, lags, cases, TF-IDF pairs, and model comparison |

The `code/` directory contains exactly seven self-contained notebooks. Notebooks 01 and 02 retain the authenticated collection and classification stages but request credentials interactively, mask them, and never save them. Notebooks 03–07 contain their analysis functions directly and make no external API calls. No separate Python helper or notebook-generation script is required.

## Reproduction Procedure

1. Install the versions in `requirements.txt`.
2. Place the `datacenter` export at `data/raw/facebook_datacenter_2024_2026.csv`.
3. Obtain your own Open States and Dartmouth Chat API keys. Notebooks 01 and 02 request them through masked `getpass` prompts; never paste a key into a code cell.
4. Start in `code/` so notebook paths stay relative.
5. Execute the seven notebooks in numerical order from clean kernels. Each notebook contains all code required for its stage. Notebook 01 resumes completed searches unless `REFRESH_COMPLETED_QUERIES` is intentionally set to `True`; notebook 02 resumes completed bill classifications.

```bash
cd code
jupyter lab
# Run notebooks 01 and 02 interactively and enter each key at its masked prompt.

# After 01 and 02 finish, notebooks 03-07 require no credentials and may also
# be run interactively or executed unattended with nbconvert.
```

Because `getpass` requires interactive entry, run notebooks 01 and 02 in Jupyter rather than unattended `nbconvert` when credentials are needed. After their processed outputs exist, notebooks 03–07 can be executed unattended.

## Paper and Website

- [Final submitted paper](output/final_paper.pdf)
- [Public project website](https://signal-state-data-centers.mikegwise22.chatgpt.site/)
- [LaTeX source and bibliography](paper/)

## Public Data Boundary

The public repository includes Open States bill data, aggregate analytical tables, figures, and the code needed to regenerate every result. Raw and processed row-level Meta Content Library records are not redistributed. Those files can contain post text, account identifiers, and account names and are excluded by `.gitignore`. To reproduce the Facebook stages, an authorized user must place their own `datacenter` export at `data/raw/facebook_datacenter_2024_2026.csv`; see [the data documentation](data/README.md).

## Primary Figures

- [Bill event map](output/figures/bill_event_map.png)
- [State legislative summary](output/figures/bill_state_summary_heatmap.png)
- [Observed advancement by policy orientation](output/figures/advancement_by_supportiveness.png)
- [Redesigned longitudinal `datacenter` attention](output/figures/facebook_longitudinal_attention.png)
- [2026 frame prevalence and engagement](output/figures/facebook_2026_frame_engagement.png)
- [Engagement-language coefficients](output/figures/engagement_language_coefficients.png)
- [State-month discourse and legislation](output/figures/state_month_discourse_legislation_heatmap.png)
- [Legislative event windows](output/figures/legislative_event_window.png)
- [Bill/Facebook frame alignment](output/figures/frame_alignment_bills_facebook.png)
- [State case studies](output/figures/state_case_studies.png)

## Required Manual Validation

No human results were fabricated. Complete these blank review files before treating measurement validity as established:

- 48 bills in `output/tables/bill_manual_validation_sample.csv`.
- 100 posts from the longitudinal corpus in `output/tables/facebook_manual_validation_longitudinal.csv`.
- 100 posts from the 2026 discourse subset in `output/tables/facebook_manual_validation_2026_discourse.csv`.
- 75 state-mention contexts in `output/tables/facebook_state_mention_manual_review.csv`.
- 50 highest exploratory bill/post pairs in `output/tables/bill_facebook_tfidf_alignment_top_pairs.csv`.

## Methodological Limitations

- The Facebook corpus is a keyword-query sample, not a random sample.
- The consistent query misses posts using only the spaced spelling.
- Query-returned volume cannot be interpreted as all Facebook attention or public opinion.
- Engagement is not support, exposure, or persuasion. Follower counts are unavailable, and pages are not a verified politician sample.
- Duplicated, syndicated, synthetic, tangential, and false-positive posts can distort the corpus.
- State mentions do not prove post origin; frame matches and topic labels require validation.
- Temporal alignment, textual similarity, and lag correlations do not establish causality, diffusion, or influence.
- The policy-orientation score is AI-assisted and requires human validation.
- Advancement is observed only as of collection time; late bills have less time to advance, and latest-action fields are not complete event histories.
- The social-augmented model is exploratory. Performance improvement means only that prior sample-specific attention adds predictive information on these folds.
