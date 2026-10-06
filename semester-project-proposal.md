# Feature-Group Reduction for PDF Malware Detection

**Recommended research title:** *Feature-Group Reduction for PDF Malware Detection: A Reproducible Evaluation on Ordinary and Evasive EMBER2024 Samples.*

**Recommendation:** select this as an eight-week empirical security project. Reproduce one published PDF malware classifier, remove selected groups of input features, retrain, and investigate whether reductions that appear acceptable on ordinary samples preserve detection of harder, initially antivirus-undetected samples.

The contribution is a controlled ablation and reproducibility study. It does not depend on inventing an algorithm or beating the baseline. Assume two or three students with basic Python/ML knowledge, roughly 20–30 combined project hours per week, a laptop with 16 GB RAM, and optional free Colab. These are planning assumptions, not measured runtime guarantees.

**1. Research problem**

The central question is: **When reducing a PDF malware detector's input features, do ordinary evaluation results predict the resulting loss in detection of evasive samples?**

A smaller feature set may preserve average classification performance while removing information needed for unusual malicious files. Conversely, some large feature groups may add little useful information. The project will measure that trade-off, including training cost, model size, prediction time, and false alarms.

Three precise research questions keep the work bounded:

1. Which of four predefined feature combinations offers the best observed trade-off between detection and computational cost?
2. Does the ordering of these combinations change between ordinary future samples and evasive samples?
3. Does a threshold selected for a 1% false-positive rate on validation data maintain a similar false-positive rate on later benign files?

**2. Why this matters to Information Security**

A detector that performs well on familiar examples can still miss the malicious files that most need detection. Feature reduction is useful only if its operational consequences are understood. Evaluating false alarms also matters: classifying a benign document as malicious has a real cost, so recall improvements should not be obtained simply by allowing more false positives.

This project concerns malware detection and security evaluation. It is not a privacy-preserving ML project. Its practical output is evidence about a specific detector and benchmark, rather than a deployable antivirus product.

**3. Current research and where the project fits**

The reviewed literature includes the following verified recent work:

| Work | What it establishes | Implication for this project |
|---|---|---|
| Joyce et al., *EMBER2024 — A Benchmark Dataset for Holistic Evaluation of Malware Classifiers*, ACM KDD 2025 | A modern, reproducible malware benchmark with standard and evasive evaluations. | Provides the base paper, features, trained models, and scripts. |
| Anthony et al., *Explainable Malware Detection with Tailored Logic Explained Networks*, 2024 preprint | Studies interpretable malware classification and the fidelity of explanations on EMBER. | Explainability is already an active research area; merely adding an explanation plot would be a weak contribution. |
| Madamidola, Ngobigha and Ez-zizi, *Detecting new obfuscated malware variants: A lightweight and interpretable machine learning approach*, *Intelligent Systems with Applications* 25, 200472, 2025; online 2024 | Combines feature reduction, random forests, and unseen-subtype evaluation on memory-derived malware data. | Lightweight models and generalisation testing are established ideas; our contribution must be described more narrowly. |
| *C-GUARD: Context-Adaptive Conformal Gating for Improving Robustness Against Evasive Windows PE Malware*, *Future Internet* 18(8), 431, 2026 | Studies recovery of evasive Windows malware under constrained additional false positives. | Evasive detection and threshold discipline are current research concerns. We will not reproduce its more complex rescue system. |

Sources: [EMBER2024 paper](https://arxiv.org/html/2506.05074v1), [Tailored LENs full text](https://arxiv.org/html/2405.03009v1), [lightweight malware paper](https://doi.org/10.1016/j.iswa.2024.200472), [C-GUARD paper](https://www.mdpi.com/1999-5903/18/8/431).

**Novelty assessment:** feature selection, LightGBM, temporal evaluation, and evasive-sample testing are already studied. The defensible contribution is a reproducible, tightly controlled measurement of **feature-group trade-offs for the PDF partition**, with explicit validation thresholds, separate time cohorts, and corrected duplicate handling. The review did not establish that no one has ever run the exact combination. Do not use “first,” “novel detector,” or “state of the art” without stronger evidence.

**4. Verified base paper**

Robert J. Joyce, Gideon Miller, Phil Roth, Richard Zak, Elliott Zaresky-Williams, Hyrum Anderson, Edward Raff, and James Holt. **“EMBER2024 — A Benchmark Dataset for Holistic Evaluation of Malware Classifiers.”** Proceedings of the 31st ACM SIGKDD Conference on Knowledge Discovery and Data Mining, 2025.

- DOI: [10.1145/3711896.3737431](https://doi.org/10.1145/3711896.3737431).
- Accessible manuscript: [full paper](https://arxiv.org/html/2506.05074v1).
- Author implementation: [FutureComputing4AI/EMBER2024](https://github.com/FutureComputing4AI/EMBER2024).
- Dataset: [joyce8/EMBER2024](https://huggingface.co/datasets/joyce8/EMBER2024).
- Released PDF classifier: [EMBER2024_PDF.model](https://huggingface.co/joyce8/EMBER2024-benchmark-models/blob/main/EMBER2024_PDF.model).

**5. What the base paper does**

The paper releases file features and labels across six formats, using 52 weeks for training and the subsequent 12 weeks for testing. It evaluates LightGBM models for several tasks; this project reproduces only **PDF malicious-versus-benign classification**. The binary benchmark uses 500 boosting rounds, 64 leaves, and a minimum of 100 observations per leaf. The paper also evaluates an initially antivirus-undetected challenge cohort against ordinary benign test files.

For the PDF-specific model, Tables 5–6 report:

| Evaluation | ROC-AUC | PR-AUC |
|---|---:|---:|
| Ordinary PDF test cohort | 0.9912 | 0.9933 |
| PDF challenge cohort plus benign test PDFs | 0.9066 | 0.7841 |

These are published results, not locally reproduced measurements. The ordinary and challenge mixtures have different malicious prevalence, so their PR-AUC difference is not a pure measure of difficulty. Our primary operating-point comparison will use recall and the achieved benign false-positive rate. [Base paper, Sections 4.1–4.3](https://arxiv.org/html/2506.05074v1).

**6. Limitations and research gap**

**Author-stated:** the benchmark models are deliberately untuned, and the authors identify evasive malware and newly emerging families as areas needing further study. [Base paper, Sections 4.4 and 4.7](https://arxiv.org/html/2506.05074v1).

**Artifact limitations verified separately:** the public repository has dependency compatibility reports and a dataset row-count discrepancy. The downloaded PDF training archive has 104,000 rows but 52,000 unique hashes; the ordinary test archive has 24,000 rows but 12,000 unique hashes. Every record in these archives occurs twice identically. No exact hash overlaps were found between training, ordinary test, and PDF challenge partitions. The duplication is consistent with an existing issue, so its discovery is not claimed as new research. Uniformly duplicated test rows can leave point metrics unchanged while falsely enlarging the apparent sample size. [Row-count issue](https://github.com/FutureComputing4AI/EMBER2024/issues/5), [dependency issue](https://github.com/FutureComputing4AI/EMBER2024/issues/13).

**Our proposed extension:** compare four feature combinations under the same training protocol, then measure whether conclusions based on ordinary data transfer to evasive PDFs. The value lies in the controlled comparison and its uncertainty, not a claim that the authors neglected all feature or robustness research.

**7. Exactly what we would implement**

Keep the classifier family fixed. Use these non-PE feature blocks from the released extractor:

| Symbol | Feature group | Dimensions |
|---|---|---:|
| G | General file information | 7 |
| H | Byte histogram | 256 |
| E | Byte–entropy histogram | 256 |
| S | String statistics and indicators | 177 |

These counts follow the inspected [feature implementation](https://github.com/FutureComputing4AI/EMBER2024/blob/main/src/thrember/features.py). The complete extractor produces 2,568 dimensions; the applicable non-PE blocks total 696. Removing PE-only zero columns is bookkeeping, not the research contribution. Verify these dimensions against the pinned code revision.

The four experimental variants are:

| Variant | Features | Dimensions | What the comparison tests |
|---|---|---:|---|
| A: baseline | G + H + E + S | 696 | All applicable feature blocks |
| B | G + S | 184 | Remove both byte-distribution blocks |
| C | G + S + H | 440 | Retain byte frequencies, omit local entropy representation |
| D | G + S + E | 440 | Retain byte–entropy representation, omit byte frequencies |

Retrain a classifier for each variant. Simply zeroing inputs to a trained model answers a different question and is not the main experiment. Preserve categorical-feature identities when columns move. Use the same hyperparameters, fitting rows, random seeds, and evaluation rows for all four variants.

The implementation contribution is a small experiment runner containing: dataset validation and deduplication; saved split manifests; explicit feature masks; repeatable training; validation-only threshold selection; cohort-specific evaluation; and tables/plots generated from saved predictions.

**8. Dataset, access, preprocessing, and limitations**

Use the public **EMBER2024 PDF partition only**. No live malware collection, raw PDF execution, VirusTotal subscription, packet capture, or sandbox deployment is required.

| Partition | Scope | Availability and interpretation |
|---|---|---|
| PDF training | 52,000 unique files: 26,000 benign and 26,000 malicious | Downloaded and parsed; approximately 123 MB compressed. Current archive stores each record twice. |
| Ordinary PDF test | 12,000 unique files: 6,000 benign and 6,000 malicious | Downloaded and parsed; approximately 29 MB compressed. Current archive stores each record twice. |
| Full PDF challenge | 805 unique malicious PDFs | Downloaded and parsed from the approximately 32 MB all-format challenge archive. This cohort spans multiple time periods. |
| Future-period PDF challenge | 236 malicious PDFs within the ordinary test period | Derived from downloaded timestamps. Use for the primary temporally aligned evasive comparison. |

Access links and published partition sizes: [official data files](https://huggingface.co/datasets/joyce8/EMBER2024/tree/main), [author dataset documentation](https://github.com/FutureComputing4AI/EMBER2024). Download sizes are compressed archive sizes and differ from expanded JSON data sizes. Reserve several GB for extracted records, arrays, and intermediate results.

Required preprocessing:

1. Record archive checksums and code/data revisions. Count rows, unique hashes, classes, and weekly records.
2. Deduplicate every partition by SHA-256; verify that repeated hashes agree on labels/features. Check train/validation/test/challenge overlap.
3. Retain hashes and timestamps for audits and splits only. Exclude labels, antivirus detection ratios, family labels, and later-derived metadata from classifier inputs.
4. Vectorize the existing JSON features with the pinned transformations. Check shapes, finite values, constants, and categorical columns. Do not regenerate features from raw files.
5. For the extension, train on the earliest 44 training weeks and validate on the last eight. Reserve the 12 ordinary test weeks and challenge cohorts for prescribed baseline reproduction and locked evaluation; never use them for tuning, feature selection, or threshold choice. Use a distinct paper-protocol reproduction run when comparing with published numbers.
6. After protocols are frozen, evaluate ordinary test files, the 236 future-period challenge positives, and—separately—the full 805 challenge positives.

Limitations: antivirus-consensus labels are imperfect; timestamps are submission times, not infection times; a historically collected, balanced benchmark is not a production prevalence estimate. Exact deduplication does not eliminate near-duplicate families across weeks. An evasive cohort is not a controlled adversarial attack with known perturbations. Results should stay within those boundaries.

**9. Implementation requirements and reproduction status**

Use Python 3.10 or newer, NumPy, pandas or Polars, scikit-learn, LightGBM, the relevant `thrember` feature code, and matplotlib/seaborn. Use a virtual environment and pin working versions. SHAP, Optuna, neural networks, and a web application are optional and unnecessary for the core question.

The repository includes training and evaluation scripts plus a binary-classification configuration. The released PDF model is approximately 3.75 MB. Those artifacts substantially reduce reproduction risk, although their existence does not establish that installation or training will succeed unchanged. [Training script](https://github.com/FutureComputing4AI/EMBER2024/blob/main/examples/train_lgbm.py), [evaluation script](https://github.com/FutureComputing4AI/EMBER2024/blob/main/examples/eval_lgbm.py), [model files](https://huggingface.co/joyce8/EMBER2024-benchmark-models/tree/main).

Source inspection found details to handle early: the generic trainer hardcodes categorical-column indices unsuitable for reduced arrays; the metadata helper builds challenge metadata from the wrong record collection; and the supplied training split does not pin its random seed. A short explicit training wrapper and direct JSON metadata parsing can avoid these helpers without changing the scientific question. Preserve these adaptations in the reproducibility log. [Model helper source](https://github.com/FutureComputing4AI/EMBER2024/blob/main/src/thrember/model.py).

**Verification boundary:** paper methods/results, code contents, and all three actual PDF data partitions were inspected. No baseline model has been trained or evaluated locally as part of this literature report. The proposed runtime and completion assessment remain conditional on the first two weeks' reproduction checks. Archive checksums and observed counts are supplied in the accompanying dataset-verification artifact.

**10. Concrete experiments and metrics**

**Experiment 1 — Baseline reproduction.** Load the released PDF model and evaluate it on properly vectorized unique ordinary/challenge records. Then train the published model configuration. Compare with the paper's PDF-specific numbers, recording software and split differences. Reproduce the repository's trapezoidal PR-AUC definition; average precision is related but not identical.

**Experiment 2 — Main feature ablation.** Train four variants with three fixed seeds: **12 model fits**. Choose a separate threshold for each fitted model using only benign validation scores, with a target validation FPR of 1%. Freeze thresholds before opening final evaluation results. Report the **achieved** ordinary-test FPR; it can drift above or below 1%.

Evaluate:

- Ordinary malware recall and future-period challenge recall at the frozen threshold.
- Benign false-positive count and rate.
- Ordinary precision, recall, F1, ROC-AUC and PR-AUC.
- Challenge recall with sample count and uncertainty; any mixed-cohort AUC requires explicitly adding the same benign test cohort.
- Training time, fixed-batch prediction time, saved model size, and input-array memory.

Challenge positives alone cannot supply precision, FPR, ROC-AUC, or PR-AUC. The primary operational comparison pairs their detection rate with the benign test FPR. Reusing the benign cohort means comparisons are paired, not independent datasets.

**Experiment 3 — Small robustness checks.** Report the full 805-file challenge separately from the 236-file future-period subset. Plot false positives by test week, with counts. If the core is complete, repeat at a 2% validation FPR target using existing predictions; do not start a large hyperparameter sweep.

Use paired bootstrap differences over unique files for model comparisons, and report seed variation separately. If related files cluster strongly by family or week, include a cluster-based sensitivity analysis where counts permit. With only 236 future-period challenge files, recall near 50% has roughly ±6 percentage points of binomial 95% uncertainty; three model seeds do not create additional independent files. Avoid claiming that a one-percentage-point difference proves superiority.

Feature-count reduction does not guarantee a smaller tree ensemble or faster inference. Measure both. Because inputs are already extracted, this project cannot establish savings in raw-PDF feature-extraction latency.

**11. Realistic possible results**

| Outcome | Valid research interpretation |
|---|---|
| A reduced variant retains ordinary and evasive recall with lower measured cost | Evidence that this feature reduction is useful in the tested setting. |
| Ordinary performance remains similar but evasive recall declines | Evidence that ordinary evaluation can conceal the security cost of reduction. |
| Both ordinary and evasive performance decline | Identifies useful feature blocks and quantifies the cost of removing them. |
| All variants perform similarly | Evidence of redundancy or insufficient sensitivity in this benchmark; report uncertainty and measured costs. |
| Test FPR rises despite the validation target | Reveals threshold transfer problems under the observed distribution change. |
| Differences are too small to resolve | An inconclusive comparison with explicit power limits; do not manufacture a winning model. |

None of these outcomes requires an accuracy improvement. A sound report can centre on a trade-off, a replication discrepancy, or a bounded negative result.

**12. Difficulty and feasibility score**

**Difficulty: Easy–Moderate implementation; Moderate overall.** Training small tabular models is straightforward. Correct cohort construction, threshold selection, reproduction accounting, and statistical interpretation require more care.

| Dimension | Score / 10 |
|---|---:|
| Implementation difficulty — higher means harder | 4 |
| Dataset availability — higher means better | 9 |
| Code availability and usefulness | 8 |
| Computational requirements — higher means more demanding | 3 |
| Research novelty | 5 |
| Strength of the experimental gap | 8 |
| Experiment-design clarity | 9 |
| Likelihood of obtaining useful findings | 9 |
| Paper/report-writing potential | 9 |
| Likelihood of completing within eight weeks | 9 |

**Overall planning score: 8.4/10.** These are judgment scores, not calibrated probabilities. The aggregate weights completion 25%, code 15%, data/ease/low compute/useful findings/gap 10% each, experiment design 5%, writing 3%, and novelty 2%; difficulty and compute are inverted as `11 − score`. The assessment prioritises finishing reproducible work over novelty. The score does not imply a 90% measured completion probability or a publication guarantee.

**13. Main risks and fallback plans**

| Risk | Mitigation | Simpler fallback |
|---|---|---|
| Dependency or vectorization failures | Pin a compatible environment; verify a small sample against the released model; correct column mapping. | Use an explicit minimal wrapper around the existing transformations and LightGBM. |
| Numerical reproduction differs | Separate evaluation of released weights from retraining; document seeds, versions, duplicates and split differences. | Reproduce the component and report explained discrepancies rather than claiming exact numerical replication. |
| Feature masks or metadata leak information | Audit named columns and hash-disjoint splits before training. | Reduce to A versus B with a fully inspected feature pipeline. |
| Little statistical power on 236 future evasive files | Report uncertainty; retain the full 805-file mixed-period cohort as supplementary evidence. | Focus on larger effects, ordinary test performance and measured costs; narrow the conclusion. |
| Training takes longer than expected | Time one fit in Week 2; keep four variants and fixed hyperparameters. | Two variants, three seeds; no tuning, SHAP, second format or extra classifier. |
| Contribution appears too incremental | Write the question and closest-work comparison before experiments. | Position it as a reproducibility/measurement paper with released splits and predictions. |

The smallest viable project is **all applicable features versus general-plus-string features**, three seeds, ordinary and evasive evaluations, frozen thresholds, measured cost, and a candid analysis. If even the baseline cannot run by the end of Week 2, the feasibility assumption has failed; do not spend the remaining six weeks repairing the environment.

**14. Eight-week execution plan**

Writing begins in Week 1 through the literature matrix and methods notes. Week 7 is for synthesis, not the first attempt to write.

| Week | What to learn | What to implement | Required output | Main blocker / response |
|---|---|---|---|---|
| 1 | Read base Sections 2–4 and related malware evaluation work; understand PDF feature groups, label provenance and temporal splits. | Download the three archives; audit hashes/classes/weeks; pin environment and exact code revision. | Annotated literature matrix; data dictionary; checksum/count report; one-page research question. | Download/schema/dependency problems. Resolve now; verify no need for raw PDFs. |
| 2 | Learn the extractor and LightGBM configuration; distinguish ROC-AUC, PR-AUC and operating thresholds. | Vectorize a small sample, then the PDF cohort; evaluate released weights; train one reproduction baseline and time it. | Baseline comparison table; saved predictions; runtime/memory estimate; discrepancy log. | Baseline fails or model scores disagree substantially. Fix feature order and label mapping before proceeding. |
| 3 | Understand paired evaluation, temporal validation and sample-size limits. | Create immutable 44/8-week development split and final cohorts; build metric and threshold functions; pilot A versus B. | Frozen experimental protocol, split manifests, planned tables, and explicit scope limits. | Weak challenge sample size or contaminated splits. Narrow claims and resolve overlap; do not tune on challenge data. |
| 4 | Learn ablation design and measurement of computational cost. | Implement all four masks, categorical-index mapping, deterministic training, and result logging. | Experiment runner; feature manifest; successful small end-to-end run for every variant. | Mask/configuration bugs. Check dimensions and categorical identities; drop optional work. |
| 5 | Learn how to read detection/cost trade-offs. | Run the 12 main fits; select thresholds on validation; freeze configurations. Perform only a small development-only tuning check if clearly necessary. | Complete raw predictions and timing records; preliminary main-results tables. | Runtime exceeds budget. Use the two-variant fallback; do not broaden the model search. |
| 6 | Learn error analysis and uncertainty interpretation. | Run locked ordinary/future-challenge/full-challenge evaluations; compute paired uncertainty; produce weekly FPR and optional second-threshold checks. | Final experiment tables, ablation plots, error breakdowns and confidence intervals. | Small differences are inconclusive. State uncertainty rather than adding favourable post-hoc experiments. |
| 7 | Learn the structure of an empirical security paper and threats-to-validity discussion. | Generate final figures; write related work, methodology, results, discussion and limitations. | Complete paper draft, reproducibility appendix, and presentation outline. | Overclaiming deployment or novelty. Align every claim with an actual experiment. |
| 8 | Learn concise presentation of a negative or mixed finding. | Rerun only unresolved checks; verify scripts on a clean environment; proofread and prepare slides/demo of results. | Final paper/report, code/configuration archive, dataset instructions, slides and submission package. | Late scope expansion. Freeze new methods; use the remaining time for correctness and communication. |

Suggested team division: one person owns data/reproduction, one owns experimental code and measurements, and one owns analysis/literature/writing. All members review split integrity and conclusions. A two-person team can combine analysis and experiment ownership.

**15. Stress-test of the recommendation**

**Why might it fail?** The largest risks are a brittle feature pipeline, an uninteresting ablation, and insufficient independent evasive files to distinguish small effects. These are more material than access to GPUs. Week-2 reproduction and Week-3 protocol checkpoints limit wasted time.

**Is the gap meaningful?** Yes, as a security evaluation question: a resource reduction should be assessed against hard failures as well as ordinary accuracy. Its value is empirical. The contribution is substantially weaker if the final work merely ranks four accuracy values without uncertainty, operating thresholds, or cost measurements.

**Has the broad idea already been studied?** Yes. The related work explicitly covers lightweight classifiers, interpretable malware detection and evasive evaluation. The proposal should be introduced as an extension of that line, not an unexplored field. An eight-week student study can be worthwhile without algorithmic novelty.

**Can we reproduce the base?** Reproducing the PDF binary-classification component is realistic because weights, configuration, features and scripts exist. Reproducing the paper's entire multi-format, multi-task benchmark is outside scope. Exact retraining equality is not assured because the release and random splits need auditing.

**Are the data accessible?** All three required public archives were downloaded and parsed successfully. Their unique counts and exact-hash separation were checked. No collection campaign is required; repeat the checks if a newer release is downloaded.

**Can the experiments run with ordinary resources?** The scoped dense input is roughly `52,000 × 696 × 4 bytes`, or 145 MB before overhead. Training requires additional memory, but this is a plausible laptop workload. Twelve CPU model fits are a finite experiment; actual timing must be established by a pilot rather than promised in advance.

**Will there be enough material for a paper?** The planned evidence includes a baseline reproduction, feature-group comparison, standard-versus-evasive results, threshold-transfer analysis, computational costs, uncertainty, and threats to validity. That supports a substantive semester report. Acceptance at an external venue depends on execution and how much new insight the results contain.

**What if the reduced model is worse?** Quantify where it loses detection, whether ordinary validation revealed the loss, and whether any cost saving justifies it. That answers the research question. Avoid redesigning repeatedly until one variant wins.

**What if all models are similar?** Report effect sizes and uncertainty. A finding that the data cannot resolve small differences is different from proving equivalence. Record any measured efficiency difference and the limits of the benchmark.

**Is there a simpler version?** Yes: A versus B only, with the same cohorts and threshold discipline. The minimum viable report remains centred on the same question.

**Is the related-work base sufficient?** Yes. The four starting works provide direct methodological context, and the base paper's references lead to prior malware benchmarks and temporal/generalisation studies. Expand the review selectively around the exact feature-reduction question rather than collecting many unrelated ML papers.

**Can it be finished in eight weeks?** Yes, conditionally: keep one file format, one classifier family, four predefined feature sets, three seeds, and no dependence on model improvement. With that scope, the project has a credible path from reproduction to experiments to writing. If stronger algorithmic novelty is a course requirement, this empirical proposal may not satisfy that requirement despite its feasibility.

**Final deliverables**

- Research paper/report with related work, exact protocol, results, uncertainty, limitations and conclusions.
- Reproduction notebook plus scripts/configurations for all main experiments.
- Data-version, checksum, duplicate-audit and split manifests; no need to redistribute the whole dataset.
- Saved prediction/metric tables and automatically generated figures.
- Presentation explaining the question, experiment, most important result and practical limits.

**Source inventory**

1. Joyce et al. (2025), [EMBER2024, ACM KDD](https://doi.org/10.1145/3711896.3737431); [accessible manuscript](https://arxiv.org/html/2506.05074v1). Base methodology and Tables 5–6; Sections 4.4/4.7 for author discussion.
2. FutureComputing4AI, [EMBER2024 repository](https://github.com/FutureComputing4AI/EMBER2024). README, feature implementation, training/evaluation scripts, configuration and model helpers.
3. Robert J. Joyce, [EMBER2024 dataset release](https://huggingface.co/datasets/joyce8/EMBER2024/tree/main) and [benchmark models](https://huggingface.co/joyce8/EMBER2024-benchmark-models/tree/main). Public data/model availability and artifact inspection.
4. EMBER2024 [issue 5](https://github.com/FutureComputing4AI/EMBER2024/issues/5) and [issue 13](https://github.com/FutureComputing4AI/EMBER2024/issues/13). Reported row-count and dependency problems; corroboration, not evidence that every paper result is invalid.
5. Anthony et al. (2024), [Explainable Malware Detection with Tailored Logic Explained Networks](https://arxiv.org/html/2405.03009v1). Related work on interpretability and feature choices; cited as a preprint.
6. Madamidola, Ngobigha and Ez-zizi (2025, online 2024), [Detecting new obfuscated malware variants](https://doi.org/10.1016/j.iswa.2024.200472). Related work on lightweight, interpretable, unseen-subtype malware detection.
7. [C-GUARD](https://www.mdpi.com/1999-5903/18/8/431) (2026), *Future Internet* 18(8), 431. Related work showing evasive detection under false-positive constraints is already active.

The public literature and artifact checks were performed on 14 September 2026. Proposed extensions, planning scores, timelines and expected outcomes are analytical judgments. They are not results reported by the cited authors or results from completed model experiments.
