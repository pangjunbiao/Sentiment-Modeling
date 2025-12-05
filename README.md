**Influence-Aware Topic Modeling for Discovering Public Transit Risk from Noisy Social Media**

This repository contains the full implementation of our importance-aware topic modeling framework for discovering and analyzing public transit risks from large-scale noisy social media data.
The project is developed for research in Intelligent Transportation Systems (ITS) and focuses on semantic risk discovery, topic coherence, and structural topic importance from user-generated content.

**Overview**

Urban public transit systems are continuously affected by service disruptions, safety incidents, overcrowding, and passenger complaints. Social media posts provide rich but noisy real-time signals of these issues. However, conventional topic models struggle with:

. short and sparse text
. High lexical noise
. Uneven user influence
. Weak and bursty engagement patters

To address these challenges, this framework introduces an influence-weighted, salience-modulated keyword graph coupled with a Poisson Deconvolution Factorization (PDF) model optimized via ADMM.

The pipeline consists of

. Data preprocessing and token normalization
. User influence modeling based on engagement, burstiness, and temporal decay
. Multi-aspect keyword graph construction (co-occurrence + influence + salience)
. Poisson Deconvolution Factorization (PDF) with ADMM optimization
. Topic decorrelation regularization for enhanced topic diversity
. Topic coherence, diversity, sharpness, and structural importance analysis
. Event-level keyword mining from high-activation post


**Dataset**

We use large-scale Weibo social media data collected from three major Chinese cities:

. Beijing
. Shanghai
. Xiamen

Each post record contains:

. Raw post content and cleaned token sequences
. User interaction statistics (followers, likes, comments)
. Post timestamps
. Comment arrival time lists
. Optional operator/official replies

Each social media post is treated as the basic analysis unit (document) in the topic modeling process.

**Model Components**

**Influence Weight Construction**

Engagement-aware post weights using:

. Interaction-based iTF
. Burstiness-based iIDF
. Hacker-News–style temporal decay
. Global normalization

**Keyword Graph Construction**

. Adjacent unordered word co-occurrence
. User influence weighting
. Word salience modulation

**Poisson Deconvolution Factorization (PDF)**

. Low-rank decomposition with structured topic interaction
. Optimized via Alternating Direction Method of Multipliers (ADMM)
. Topic–word matrix 𝑈, topic interaction matrix 𝐻, and structural importance matrix 𝐴

**Topic Regularization**

. Topic decorrelation penalty controlled by 𝛾

**Evaluation**

. NPMI, 𝐶𝑣 coherence
. Topic Diversity (TD)
. Topic Sharpness (entropy & top-mass)
. Topic structural importance via diagonal 𝐴
