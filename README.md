**Sentiment Escalation–Aware Topic Modeling for Transit Risk Prioritization**

This repository contains the full implementation of our framework for weak-signal detection and early-warning of emerging transit risks using city-scale social media data. The project was developed as part of our Intelligent Transportation Systems (ITS) research.

**Overview**

Our framework detects weak but escalating complaints (e.g., driver misconduct, service delays, safety hazards) from Weibo posts, and aligns them with operator incident logs for validation.
The methodology integrates:

. Data Preprocessing (cleaning, tokenization, weighting)
. User Influence Modeling (weighting posts by author credibility and reach)
. Multi-Modal Keyword Graph Fusion
. Poisson Deconvolution Factorization (PDF) with ADMM optimization
. Sentiment Escalation Modeling using KL divergence on temporal distributions
. Dynamic Topic Ranking combining frequency, centrality, attention, influence, and escalation

**Dataset**

Weibo datasets were collected from three Chinese cities (Beijing, Shanghai, Xiamen) over multiple months. Each record includes:

. Post content and tokenized words
. User metadata (ID, follower/interaction stats)
. Timestamp and location fields
. Official operator replies (when available)

**Run the full pipeline**

python main.py

**Evaluation Metrics**

We report multiple metrics for early-warning quality:

. ROC-AUC / Average Precision (AP)
. Precision@K (P@5, P@10)
. Lead-time (first-hit@K in hours)
. Real time analysis 
