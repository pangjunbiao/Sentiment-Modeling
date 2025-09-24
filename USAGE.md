<h1>How to Use This Software</h1>
<p>This repository implements a full weak-signal early-warning pipeline for transit risk from Weibo:</p>
<pre>
1. <b>Data</b> → 2. <b>Influence weights</b> → 3. <b>Keyword graph</b> → 4. <b>PDF model</b> → 5. <b>Sentiment escalation</b> → 6. <b>Topic severity &amp; ranking</b> → 7. <b>Evaluation &amp; figures</b>
</pre>


---

## Project Flow

The pipeline follows the logical flow shown in the diagram:

### Stage A: Data & Preprocessing
- **Input**: Weibo posts + user metadata.  
- **Cleaning**: Text tokenization and stopword removal.  
- **Influence weighting**: Each post is assigned a dynamic influence score \( Q_i(t) \) using i-TF, i-IDF, and temporal decay functions.  

### Stage B: Graph Construction & PDF
- **Keyword Graph** \( W_t \): Build per-window, weighted by user/post influence.  
- **PDF Model**: Apply Poisson Deconvolution Factorization (PDF):
  \[
  W_t \approx U A_t U^\top + H_t + \mu S_t
  \]
  - \( U \): topic–keyword basis  
  - \( A_t \): topic attention matrix  
  - \( H_t \): background noise  
  - \( S_t \): sentiment term  
- **Optimization**: Solved via MM (for \( U, A_t \)) and ADMM (for \( H_t \)).  

### Stage C: Sentiment Escalation
- **Sentiment Estimation**: Use SnowNLP + kernel density estimation (KDE) to model daily distributions in \([0,1]\).  
- **Escalation Signal**: Compute inter-day shifts via KL divergence:
  \[
  E(t, k) = KL(p_t \;\|\; p_{t+1})
  \]

### Stage D: Prioritization & Early Warning
- **Severity Scoring**: Combine multi-factor scores:
  - frequency,  
  - structural centrality,  
  - topic attention,  
  - influence,  
  - sentiment escalation.  
- **Smoothing**: Apply exponential moving average (EMA) to produce stable severity curves.  
- **Early Warning**: Topics crossing a threshold are flagged, producing:
  - ranked topic lists,  
  - alarm times (first-hit detection).  

---

<h1>Project Layout</h1>
<pre>
config/
└── config.yaml                # Central switches: data paths, K, betas, μ mode, eval settings

data/
├── raw/                       # (Optional) raw CSVs (beijing.csv, shanghai.csv, xiamen.csv)
└── processed/                 # Processed inputs used by the pipeline (…_processed.csv)

src/
├── data/
│   └── process.py             # (Optional) raw → processed
├── influence/
│   └── weights.py             # i-TF, i-IDF, HackerNews decay → post weights Q_i(t)
├── graph/
│   └── cooc.py                # Build influence-weighted keyword co-occurrence W_t
├── models/
│   ├── pdf_init.py            # Initialize U, A_t
│   └── pdf_admm.py            # Sentiment-augmented PDF (MM + ADMM)
├── sentiment/
│   ├── sentiment.py           # SnowNLP, KDE on [0,1], KL(p_t || p_{t+1}) → escalation
│   └── sentiment_visualization.py
├── ranking/
│   ├── severity.py            # Multi-factor severity S(t,k): freq/centrality/attention/escalation
│   └── ablation.py            # β-ablation utilities
├── eval/
│   └── metrics.py             # AUC, AP, P@K, first-hit@K (hours), K/M sensitivity
└── utils/                     # Loaders, logging, plotting helpers

outputs/
├── artifacts/                 # Arrays & CSVs (U, A, S, E, centrality, influence, time_index.csv…)
├── reports/                   # Per-topic metrics, summaries, ablations, early-warning CSVs
├── figures/                   # Plots (β-ablation figure, etc.)
├── sensitivity_analysis/      # K/M/h sensitivity results
└── logs/                      # Pipeline logs
</pre>



## Quickstart: one-line run

python main.py --config config/config.yaml --run all

## This executes the full pipeline:

outputs/artifacts/: U_KxV.npy, A_TxK.npy, S_TxK.npy (severity), E_TxK.npy (escalation), centrality, frequency, time_index.csv, plus μ-variant dumps (mu_global_*, mu_none_*, mu_perwindow_*).

outputs/reports/: eval_model_* CSVs with per-topic rows and run summaries.

outputs/figures/: β-ablation figures, etc.

outputs/sensitivity_analysis/: K/M sensitivity tables.

## Switching μ Mode

Inside main.py, set the μ variant for baselines:

"mu_mode="per_window",  # "none" | "global" | "per_window""

1. mu_mode="none" → decoupled baseline (no sentiment).

2. mu_mode="global" → shared global μ across all windows.

3. mu_mode="per_window" → separate μ_t per time window.

## What to Look At (Outputs → Paper Tables)

**Main Early-Warning Table**  
- File: `outputs/reports/eval_model_summary.csv`  
- Columns: `mean_auc_S`, `mean_ap_S`, `mean_p_at_k`, `mean_first_hit_topk_h`

**β-ablation**  
- File: `outputs/reports/beta_ablation_summary.csv`  
- Columns: `tau_all`, `tau_topK`, `mean_lead_days`, `mean_lead_hours`, `early_rate`

**Sensitivity**
- **K topics**: `outputs/sensitivity_analysis/K_sensitivity/results/*.csv`
- **M window width**: `outputs/sensitivity_analysis/M_sensitivity/results/*.csv`

