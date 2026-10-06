# Phishing / malicious-URL literature and feasibility evidence

Research date: 14 September 2026. Findings below distinguish paper claims, inspected implementation, dataset facts, and our proposal. No remote repository code was executed and no URLs inside datasets were visited. Primary publisher metadata and author code were checked; the PhiUSIIL full text was readable through a third-party mirror of the actual paper, so its methodological details were cross-checked against the author notebook. The publisher page itself exposed the abstract/highlights but blocked direct full-text access. This is not an empirical reproduction; that belongs in project weeks 1–2.

## Recommendation

**Candidate: Budgeted Feedback for Incremental Phishing Detection: A Reproducibility Study of PhiUSIIL.**

Prefer this bounded experimental audit over another classifier/SHAP/feature-selection accuracy comparison. Delayed labels and active learning are established ideas; the contribution would be an explicitly scoped replication and comparison in this published phishing ensemble, not a new general learning algorithm or a claim that nobody studied label budgets. Likely rank below a cleaner small-data malware base if that base has complete scripts and true timestamps. Moderate difficulty; 8 weeks plausible with scope limits.

## Base paper — verified identity, methodology, results, author limitations

Arvind Prasad and Shalini Chandra. **PhiUSIIL: A diverse security profile empowered phishing URL detection framework based on similarity index and incremental learning.** *Computers & Security* 136 (January 2024), article 103545; online October 2023. [DOI/publisher](https://doi.org/10.1016/j.cose.2023.103545). [Author code](https://github.com/arvindbitm/PhiUSIIL). [UCI identity cross-check](https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset). [Full manuscript mirror](https://www.scribd.com/document/1046276748/PhiUSIIL-ResearchPaper).

The system combines a URL-similarity screen with an incremental ensemble of Bernoulli naive Bayes, Passive-Aggressive, and SGD classifiers. It selects 30 features from URL, webpage, and derived signals; three voting profiles trade permissiveness against blocking. The 235,795-record dataset supports test-then-train evaluation and pretraining fractions from 10% to 90%. Reported accuracy is 99.24% fully incremental and 99.79% with pretraining; Table 7 gives 0.99792 accuracy and 0.99817 F1 for the 90:10 experiment. Metrics include precision, sensitivity, F1, MCC, accuracy, and training time. These are the authors’ measurements under their protocol, not expected student results or frozen-test estimates.

**Author-stated limitations:** Section 6 warns that continued updating can slow responsiveness and overspecialize the model. Suggested directions include regularization, removing redundant training data, and optimizing updates. The authors also discuss limitations for executable-download URLs and adversarial vulnerability; those extensions are outside the recommended scope.

Locations inspected: paper §4.2, Algorithm 3, §5.6/Table 7, §6 (printed pp. 9–15); mirror lines 657–708, 1053–1077. Above paper-derived prose intentionally stays below ~200 words excluding bibliographic metadata.

## Code inspection — what is actually present

Repository tree contains six notebooks and no README/environment lockfile/license apparent in inspected tree. Key files: [MainModel_SplitBased_OneTime90.ipynb](https://github.com/arvindbitm/PhiUSIIL/blob/main/MainModel_SplitBased_OneTime90.ipynb), [IdentifyClassifiers.ipynb](https://github.com/arvindbitm/PhiUSIIL/blob/main/IdentifyClassifiers.ipynb).

The main notebook reads a local `dataset/PhishingDataset.csv`, hardcodes 30 numeric feature names, randomly takes 90% for pretraining, then predicts each remaining row before calling `partial_fit(X,Y)` using its true label. It reports voting profiles and metrics with legitimate as class 1. The exact main split has no fixed random seed. It uses deprecated `DataFrame.append` and local output paths; straightforward modernization is needed. The notebook does not recreate the URL-similarity first stage. Therefore reproduce **the published incremental component**, not claim full end-to-end PhiUSIIL reproduction. Inspecting source is stronger evidence than simply seeing a GitHub link, but no runtime success was established.

Saved unexecuted code text: `research/phishing_artifacts/MainModel_SplitBased_OneTime90.txt` and `IdentifyClassifiers.txt`. Tree provenance: `arvindbitm_tree.json`.

## Dataset and preprocessing

[Official UCI PhiUSIIL](https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset): 235,795 rows; 134,850 legitimate, 100,945 phishing; 54 declared features (56 CSV columns including filename and label); tabular CSV with raw URL/domain/title and precomputed numerical URL/webpage features. Download shown as 14.7 MB compressed, CSV 54.2 MB. CC BY 4.0; no account indicated. UCI says no missing values. Labels: 1 legitimate, 0 phishing; discard FILENAME.

For implementation: check types/missingness/duplicates, keep an explicit label mapping, retain domains solely for split audits, create stable row identifiers/hashes, and validate every required baseline feature. No live webpage extraction is needed. No collection timestamp is supplied in the visible schema; random row order cannot establish temporal drift. Precomputed population-derived features require provenance care; retain the paper’s feature set for replication and discuss their limitations rather than declaring all high-importance features leakage.

**Our proposed preprocessing controls:** learn any imputation/scaling/feature selection exclusively from pretraining/tuning data; never from stream labels not yet revealed. First match the existing baseline without adding scaling. If a scaled variant is tested, freeze its transformer from warm-up data and label it a separate ablation. Exact duplicate removal and registrable-domain grouping are robustness checks, not claimed novelty. Use a pinned public suffix list for registrable domains; a `Domain` string is not automatically an eTLD+1.

## Current research and nearest prior work

1. **Rashid, Fariza; Doyle, Ben; Han, Soyeon Caren; Seneviratne, Suranga (2024). Phishing URL detection generalisation using Unsupervised Domain Adaptation.** *Computer Networks* 245, 110398. [DOI/full primary content](https://doi.org/10.1016/j.comnet.2024.110398). Methods §§4, 6–8 and discussion §9 were inspected through indexed full text. Three URL corpora: ISCX, EBBU, and 92,500 HISPAR–Phishstats URLs. They identify source-dependent length/prefix shifts, remove URL prefixes and alter source path-length distributions to align with unlabeled target data. Cross-dataset F1 gains average 0.06 and reach roughly 0.2 in some settings. They explicitly acknowledge not modeling feature intercorrelations. No functioning official code link was verified; their then-promised dataset release should not be assumed. This establishes that generic cross-dataset URL generalization is already an explicit research line.

2. **Dalton, Thomas; Gowda, Hemanth; Rao, Girish; Pargi, Sachin; Hadj Khodabakhshi, Alireza; Rombs, Joseph; Jou, Stephan; Marwah, Manish (2025). PhreshPhish: A Real-World, High-Quality, Large-Scale Phishing Website Dataset and Benchmark.** Credible industry-authored arXiv preprint, arXiv:2507.10854; no peer-reviewed venue verified. [Full text](https://arxiv.org/html/2507.10854v1), [official repository](https://github.com/phreshphish/phreshphish), [official data](https://huggingface.co/datasets/phreshphish/phreshphish). Sections 3–5 and appendices C–E inspected. It addresses poor data quality, train-test similarity leakage, and unrealistic phishing prevalence. Its v1 contains ~372k HTML–URL pairs and temporal, similarity-filtered tests plus hundreds of low-base-rate benchmarks. Linear/FNN/GTE/LLM baselines show deteriorating precision at low prevalence. Authors warn scraped phishing pages may be systematically easier than excluded pages. This makes base-rate and leakage checks necessary controls, not original contributions by themselves.

3. **Ahamed, Tanvir; Kakon, Shawon Chakrabarty; Farid, Fahmid Al; Uddin, Jia; Abdul Karim, Hezerul Bin (2026). An integrated evaluation protocol for adversarial robustness, generalization, and explanation stability in URL-based phishing detection.** *Frontiers in Computer Science* 8:1834407; published August 31, 2026. [DOI/full text](https://www.frontiersin.org/journals/computer-science/articles/10.3389/fcomp.2026.1834407/full). §§3.2–3.3 and §4 inspected. It evaluates LR, XGBoost, CharCNN and BERT on PhiUSIIL with domain-disjoint splits; attacks, cross-source checks and explanation stability are already included. Test accuracy ranges 0.9962–0.9984. Its external PhishTank test initially pairs phishing from a new source with held-out PhiUSIIL negatives; an additional Majestic benign check is discussed. No GitHub link was found in the article text. This directly weakens novelty of “PhiUSIIL + domain split + SHAP + robustness.”

4. **Olímpio, Gilberto; Camargos, Lasaro; Miani, Rodrigo Sanches; Faria, Elaine Ribeiro (2023). Model update for intrusion detection: Analyzing the performance of delayed labeling and active learning strategies.** *Computers & Security* 134, 103451. [DOI](https://doi.org/10.1016/j.cose.2023.103451), [publisher content](https://www.sciencedirect.com/science/article/pii/S0167404823003619). Publisher introduction/method snippets/conclusion inspected, not full tables. Studies delayed labels and active selection on CIC-IDS2017/CSE-CIC-IDS2018; reports active learning can retain baseline-comparable performance with fewer labeled instances. This is adjacent security precedent, so the proposed PhiUSIIL study transfers an established evaluation question rather than inventing active learning.

5. Optional methodological reading: **Amekoe et al. (2024), Evaluating the Efficacy of Instance Incremental vs. Batch Learning in Delayed Label Environments: An Empirical Study on Tabular Data Streaming for Fraud Detection**, arXiv:2409.10111. [Full text](https://arxiv.org/html/2409.10111v1), [code](https://github.com/anselmeamekoe/DelayedLabelStream). §§3–4 inspected. It separates first prediction from delayed label arrival, compares per-instance and batch models, and measures memory needed while waiting for labels. Simulation includes 10% warm-up and Poisson delays; its real fraud dataset has ~6.5M records and is unsuitable as this project’s data. Borrow evaluation discipline, not its full benchmark suite.

## Exact student contribution and research problem

**Question:** For the reproduced incremental ensemble, how much detection utility is lost when labels are available for only a small fraction of observations, and can selection based on model disagreement improve the utility–update-cost trade-off versus random selection?

**Why it matters:** A deployable adaptive detector cannot casually assume every newly observed URL immediately receives a trustworthy label. Every update also consumes time. A reproducible estimate of these trade-offs informs how one might allocate analyst feedback. No real analysts or user study is required.

**Our identified extension (not author claim):** The inspected notebook supplies each stream label immediately to all models. Replace this with an explicit label-query policy and a finite delay queue, holding model family/features fixed. This is an assumption audit plus small algorithmic comparison. We did not establish worldwide absence of the exact experiment; novelty should be pitched conservatively as a reproducible phishing case study.

**Keep scope bounded:** Mandatory experiment is label budget, with delay only one follow-up stress test. Do not combine this with LLMs, a browser extension, new crawling, federated learning, a custom drift detector, or many classifiers.

## Concrete experimental design

- First reproduce the notebook’s 90% warm-up / 10% prequential stream condition with fixed seeds. Record unavoidable version changes and exact label convention. Do not compare its prequential result with an unrelated frozen 80:20 test as if protocols matched.
- Main controlled experiment uses a smaller fixed warm-up, e.g. 10%, a separate tuning prefix or disjoint tuning partition, and remaining data in a predeclared simulated stream. Three fixed stream seeds; same seed/order across policies. A cap of ~50k stream rows is acceptable for development; final main results use the full chosen stream if timing allows.
- Policies: frozen ensemble; all-label immediate update; random feedback at budgets 10% and 25%; disagreement-prioritized feedback at the same budgets. Main primary metric: phishing-class F1/recall and benign false-positive rate; also MCC, macro-F1, number of labels requested, updates, update time and peak pending-buffer size. Hard votes have only four score levels; avoid treating them as calibrated probabilities or promising meaningful fine-grained ROC/PR threshold curves.
- A practical online query rule: score disagreement before revealing labels, request it subject to a fixed block budget and randomized exploration; use the same maximum number of labels for random and disagreement methods. If using block ranking, buffer the block and delay every policy’s updates consistently. Never look at true labels to select samples.
- Follow-up stress test: compare random vs disagreement at one fixed budget (25%) with delays 0, 100, 1,000 **records**, not days. Predictions are saved once, before updates. Pending labels must remain inaccessible to training/query logic. All labels may be available later to an isolated evaluator, including nonqueried ones, to avoid biased evaluation of only queried cases.
- Ablations: one voting profile (BestSecurity) for all main experiments; optionally contrast three profiles only on the chosen operating point. Check both ordinary random and domain-grouped warm-up/stream separation if enough time. Any ordering by domain/URL-length is a designed distribution-shift stress test, not observed chronological drift.
- Avoid large combinatorics: main 6 policies × 3 seeds = 18 runs; one delay stress test adds 2 methods × 3 delays × 3 seeds = 18 runs, with shared zero-delay results if applicable. Tune only a small grid on development data. Freeze choices before main stream evaluation.

## What results can legitimately support

Budgeted updating may match all-label updating, lose recall, or worsen benign false positives. Disagreement querying may be no better than random because its labels are biased toward difficult examples. Frozen models may already be nearly optimal on a highly separable static corpus. Each outcome is informative if uncertainty and identical budgets/order are reported. A null result supports a bounded conclusion about this dataset/protocol, not “incremental learning never works.” A worse proposed policy still yields label-cost curves, error breakdowns and a careful replication report.

## Requirements, risk and fallback

Python, NumPy, pandas, scikit-learn, matplotlib/seaborn; optional psutil for memory and tldextract for group audits. CPU laptop, preferably 8–16 GB RAM; no GPU required. We verified code structure/data metadata, not runtime. Row-by-row pandas appends must be replaced by array/list logging for practical speed while preserving predictions/update order. Avoid hiding failed rows under broad exception handling.

Main risks: old incomplete notebook packaging, unspecified seed, result mismatch, no true timestamps, unusually easy/precomputed dataset features, no measurable effect at 90% warm-up, and modest novelty. These are reasons to rank it moderate, not a guaranteed #1. Abundant related work supports writing, but publishability cannot be promised.

**Fallback 1:** remove query heuristics and delays; compare frozen versus every-row versus fixed-interval updates with identical data/feedback, including runtime and prediction stability. **Fallback 2:** reproduce three voting profiles and pretraining fractions with seed sensitivity, label-semantic correction, and honest protocol discussion. This remains a semester replication/report, with weaker original contribution. Deadline: if the incremental component is not producing credible baseline behavior by end of week 2, switch to a simpler topic rather than spend half the semester fixing packaging.

## Eight-week outline if selected among top three

| Week | Learn / implement | End-of-week output | Main blocker |
|---|---|---|---|
| 1 | Read base §4–6; understand prequential evaluation and labels; inspect CSV/source; pin environment | source notes, data manifest, reproducibility checklist | notebook assumptions, missing dependency versions |
| 2 | Modernize notebook without changing algorithm; replicate 90:10 component and verify label metrics | baseline reproduction table, per-record predictions, timings | cannot reproduce or explain discrepancy |
| 3 | Validate update timing; construct isolated evaluator; pilot warm-up/budget sizes | frozen protocol and 2–3 research questions | easy dataset leaves tiny effect |
| 4 | Implement random and disagreement budgets, deterministic query logs | audited policy runner and matched-budget pilot | inadvertent access to hidden labels |
| 5 | Run main 18 controlled runs; minimal tuning from development only | main metric/cost tables and seed variability | sequential runtime; query-policy imbalance |
| 6 | Run one delay stress test and one robustness check | delay curves / ablation table | no timestamps, interpret synthetic stream carefully |
| 7 | Analyse false positives/negatives and disagreements; write methods/results/related work | draft paper and plots | overclaiming external validity |
| 8 | Rerun only unresolved checks; polish report and presentation | final paper, slides, reproducible artifact | late scope expansion |

## Other candidate screened out as primary

PhreshPhish is better for true temporal tests, but current official data card describes 666,315 rows and about 36.6 GB, with 2026 additions—do not mix these counts with v1-paper results. Inspected author repo contains baseline, benchmark, cleaning, and train/test code. A linear baseline is available, but HTML extraction/vocabulary can be large, scripts have local-format assumptions, and some repository README links are stale. A 10k–30k archived-HTML subset is possible but only a scaled reproduction; URL-only use would change the paper’s main input. For a strict 8-week first project, PhiUSIIL’s small CSV is safer. A future extension can evaluate a chosen URL-only model on PhreshPhish metadata if projected-column access is first proven; it should not be a promise here.
