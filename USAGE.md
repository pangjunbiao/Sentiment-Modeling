<h1>How to Use This Software</h1>
<p>This repository implements a full influence-aware topic modeling pipeline for public transit risk discovery from Weibo:</p>
<pre>
1. <b>Data</b> → 2. <b>Influence weights</b> → 3. <b>Keyword graph</b> → 4. <b>PDF model</b> → 5. <b>Topic decorrelation &amp; structural learning</b> → 6. <b>Event keyword mining &amp; topic importance</b> → 7. <b>Evaluation &amp; figures</b>
</pre>



---

## Project Flow

The pipeline follows the logical flow shown in the diagram:

### Stage A: Data & Preprocessing
- **Input**: Weibo posts + user metadata.  
- **Cleaning**: Text tokenization, normalization, and stopword removal.  
- **Document Unit**: Each social media post is treated as a document.  

### Stage B: User Influence Modeling
- **Influence Weight Construction**: Each post is assigned an influence weight $$w_p$$ based on:
  - Interaction-based iTF,
  - Burstiness-based iIDF,
  - Hacker-News–style temporal decay,
  - Global max-normalization.
- **Output**: Normalized post influence weights used in graph construction.

### Stage C: Keyword Graph Construction
- **Keyword Graph** $$W$$: Constructed using:
  - Adjacent unordered word co-occurrence,
  - Post influence weighting $$w_p$$,
  - Word salience modulation $$s_i$$.
- **Graph Definition**:
  $$W_{ij} = s_i s_j \sum_p w_p \, \delta_{ij}^{(p)}$$

### Stage D: Poisson Deconvolution Factorization (PDF)
- **Model**: Apply Poisson Deconvolution Factorization:
  $$W \approx U A U^\top + H$$
  - $$U$$: topic–keyword matrix  
  - $$A$$: diagonal topic-importance matrix  
  - $$H$$: background/noise interaction matrix  
- **Optimization**:
  - Majorization–Minimization (MM) for $$U$$ and $$A$$,
  - ADMM for $$H$$.
- **Regularization**: Topic decorrelation controlled by $$\gamma$$.

### Stage E: Topic Evaluation & Analysis
- **Semantic Quality**:
  - NPMI,
  - $$C_v$$ coherence,
  - Topic Diversity (TD).
- **Topic Sharpness**:
  - Mean entropy,
  - Top-$m$ probability mass.
- **Structural Importance**:
  - Topic ranking using diagonal entries of $$A$$.

### Stage F: Event Keyword Mining
- **Post Assignment**: Each post is assigned to its dominant topic.
- **Keyword Extraction**: Event-level keywords are mined from top-activation posts per topic.
- **Output**: Interpretable transportation-related event descriptors.

### Stage G: Visualization & Reporting
- **Outputs**:
  - Topic coherence and diversity tables,
  - Topic sharpness curves,
  - Topic importance rankings,
  - Event keyword tables.


---

<h1>Project Layout</h1>
<pre>
configs/
├── experiments_ablation.yaml      # Ablation experiment configurations
├── experiments_baselines.yaml     # Baseline experiment configurations
├── model.yaml                     # Model hyperparameters (PDF, ADMM, γ, etc.)
└── paths.yaml                     # Centralized data & output paths

data/
├── raw/                           # Original city-wise social media CSV files
├── interim/                       # Intermediate merged/cleaned files
└── processed/                     # Final processed data used by the pipeline

logs/
├── .placeholder
└── pipeline.log                   # Runtime pipeline logs

src/
├── analysis/
│   └── plot_training_loss.py      # Training diagnostics and convergence plots
│
├── data_pipeline/
│   ├── combine_cities.py          # Merge multi-city datasets
│   ├── influence_weights.py       # iTF, iIDF, time decay → post influence weights w_p
│   ├── loader.py                  # Unified data loading interfaces
│   ├── preprocessing.py          # Tokenization, cleaning, filtering
│   └── vocab_builder.py           # Vocabulary construction
│
├── evaluation/
│   ├── coherence.py               # NPMI computation
│   ├── diversity.py               # Topic diversity metric
│   ├── report.py                  # Aggregated evaluation reports
│   ├── top_topic_posts.py         # Representative post extraction
│   ├── topic_event_keywords.py   # Event keyword mining
│   ├── topic_importance.py        # Structural topic importance via A_k
│   ├── topic_sharpness.py         # Entropy and top-m mass sharpness
│   └── topics_io.py               # Topic result I/O utilities
│
├── experiments/
│   ├── ablations/
│   │   ├── run_no_decay.py
│   │   ├── run_no_gamma.py
│   │   ├── run_no_h.py
│   │   ├── run_no_salience.py
│   │   ├── run_no_weights.py
│   │   └── run_plain_graph.py
│   │
│   └── baselines/
│       ├── run_gsdmm.py
│       ├── run_hdp.py
│       ├── run_lda.py
│       └── run_nmf.py
│
├── graph/
│   ├── keyword_graph.py           # Influence- & salience-weighted keyword graph
│   └── salience.py                # Word salience estimation
│
├── model/
│   ├── admm_h.py                  # ADMM optimization for H
│   ├── initialization.py         # Model initialization
│   ├── inspect_topics.py         # Topic inspection utilities
│   ├── losses.py                 # Objective functions
│   ├── poisson_topic_model.py    # Core PDF model
│   ├── trainer.py                # End-to-end training loop
│   └── updates_u_a.py             # MM updates for U and A
│
├── utils/
│   └── logging_utils.py           # Unified logging tools
│
└── main.py                        # Full pipeline entry point
</pre>



## Quickstart: one-line run

python main.py --config config/config.yaml --run all



