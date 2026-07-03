# The AI-Architecture Question — Transformers, LLMs, and Whether to Train Our Own

This is the forward-looking companion to [techniques.md](techniques.md) and
[edtech-landscape.md](edtech-landscape.md). It takes on the hypothesis behind this whole folder and
tries to answer it honestly with the evidence that exists:

1. **Will a transformer detect interaction patterns better than the RNN/LSTM sequence models the
   field relied on?**
2. **Can a *pretrained frontier LLM* do this off-the-shelf via prompting, or do we need to *train our
   own* model?**

These are two independent axes, and the evidence answers them differently. The honest bottom line up
front:

> **Transformers beat RNNs on *large-scale* behavior data, but the advantage shrinks — sometimes to
> nothing — on the *small* datasets typical of education.** And a **pretrained LLM prompted on raw
> logs is a genuine, now-publishable option for flexible, low-volume, semantic detection, but it is
> generally *weaker* than a fine-tuned specialist on well-defined, high-throughput tasks.** This is
> almost exactly the tradeoff the [detector spec](https://github.com/concord-consortium/collaborative-learning/pull/2742)
> already assumes — LLM as the flexible starting point, trained ML/rules as the optimization — so
> the evidence *supports the spec's instinct* more than it supports "just switch to transformers."

> **Honesty tiers.** *Solid* = replicated/landmark. *Sound-but-unproven* = one/few studies. *Gap* =
> thin. A few citations here come from the research scan and were **not** independently re-verified
> by me; those are flagged and listed in [papers-to-obtain.md](papers-to-obtain.md) and
> [working-notes.md](working-notes.md).

---

## 1. Axis one: does the transformer actually beat the RNN?

The answer splits by **how much data you have** — which turns out to be the whole story.

### Outside education (big data): yes, fairly cleanly

On web-scale behavior sequences, self-attention beats recurrent and convolutional models:

- **SASRec** (Kang & McAuley, ICDM 2018) — a self-attention sequential recommender that
  "outperforms various state-of-the-art sequential models (MC/CNN/RNN-based)." *(Solid; verified.)*
- **Behavior Sequence Transformer** (Chen et al., Alibaba, 2019) — a Transformer over the user's
  click sequence beat Wide&Deep and Deep Interest Network on a Taobao production dataset. *(Solid;
  verified.)*

Why: these tasks have **hundreds of millions of interactions**, and attention's flexibility pays off
when there's enough data to fit it. This is the regime the hypothesis implicitly imagines.

### Inside education (small data): mixed, and instructive

Knowledge tracing (KT) is the natural test bed — it's where education put RNNs and then transformers
head-to-head — and the result is a **cautionary tale**:

- **DKT** (Piech et al., 2015) put an **LSTM** on student interaction sequences and reported
  substantial gains over Bayesian Knowledge Tracing. *(Solid; verified.)*
- **SAKT** (Pandey & Karypis, 2019) brought self-attention to KT and reported +4.43% AUC over prior
  methods — *but* its own successors couldn't reproduce a clean win. *(Verified.)*
- **AKT** (Ghosh et al., 2020) reports that **SAKT does *not* outperform the RNN-based DKT** (or the
  memory-network DKVMN) in their experiments. Their diagnosis is the key sentence for us:

  > *"response datasets are several magnitudes lower than natural-language datasets and are less
  > likely to benefit from highly flexible and large-scale attention models."*

  AKT only *does* beat prior KT (by up to ~6% AUC) after adding a **domain-specific** monotonic
  attention with an exponential forgetting decay — i.e. **naive transfer of the NLP transformer was
  insufficient; the win came from education-specific redesign.** *(Solid; verified.)*
- **pyKT** (Liu et al., NeurIPS 2022) is the decisive sober check: under a corrected evaluation that
  removes label leakage, *"the improvement of many [deep KT] approaches is minimal compared to the
  very first DLKT model proposed by Piech et al."* — i.e. much of the reported transformer-over-RNN
  progress was partly an **evaluation artifact.** *(Solid; verified from abstract.)*

And beyond knowledge tracing, on the task closest to ours — **affect detection from raw interaction
logs** — the one head-to-head we have points the same way. **Jiang et al. (2018)** compared expert
feature engineering against deep neural networks for detecting affect in the open-ended Betty's Brain
environment (the very detectors DDCI uses) and found a **tradeoff, not a neural win: feature
engineering was better for a single-optimized-threshold decision — exactly the real-time
*trigger/intervention* case we have — while deep nets were better only when using full model
confidence** (for offline discovery analyses). *(Solid; verified from the primary text.)*

**Implication for us.** Our interaction data is far closer to KT's scale (thousands of students,
not billions of clicks) than to Taobao's. So the KT lesson transfers: **a from-scratch transformer
is not a free win on our data volume**, and if we train one, the gains will likely come from
*domain-specific design*, not from the architecture alone. This is a direct, evidence-based caution
against the simplest reading of the hypothesis.

## 2. Axis two: prompt a pretrained LLM, or train our own?

This is the more decision-relevant axis, because the spec's premise is "the **LLM detector** is the
starting point." The evidence is genuinely two-sided.

### The case that pretrained-and-prompted can work

- **Pretrained LLMs are surprisingly capable zero-shot on non-language numeric sequences.** "Large
  Language Models Are Zero-Shot Time Series Forecasters" (Gruver et al., NeurIPS 2023,
  arXiv:2310.07820) shows GPT-3/LLaMA-class models forecasting time series *without fine-tuning*, at
  a level comparable to purpose-built models — *if* the sequence is serialized carefully into
  tokens. *(Sound; not independently re-verified — flagged.)*
- **In education, prompting an LLM on raw clickstreams is already a published pipeline.**
  **ClickSight** (Radmehr et al., AIED 2025) feeds raw student clickstreams + a list of candidate
  learning strategies to an LLM and gets textual interpretations — **in-context, no fine-tuning.**
  This is essentially our "LLM detector" and it exists today. *(Sound-but-unproven; verified it
  exists and is prompt-based.)*

The appeal is exactly what the spec banks on: **flexibility without a training set.** A researcher
describes a new "interesting" pattern in words, and the detector exists immediately — no labeled
corpus, no retraining. For a construct that changes per study and starts with *zero* labeled
examples, this is the only approach that even starts.

### The case that you'll still need to train (or at least fine-tune)

- **Fine-tuned specialists beat zero-shot LLMs on well-defined classification.** A 2024 comparison
  (arXiv:2406.08660) found fine-tuned BERT-family models (125–435M params) **significantly
  outperform** zero-shot GPT-3.5/GPT-4/Claude-3-Opus across classification tasks — sometimes hugely
  (e.g. .94 vs .57 F1). Small fine-tuned models with a few hundred labels win. *(Sound; not
  re-verified — flagged.)*
- **On log/event data specifically, the strong results are fine-tuned, not prompted.** **LogLLM**
  (Guan et al., 2024, arXiv:2411.08561) reaches top log-anomaly performance by **fine-tuning** a
  BERT+Llama stack (QLoRA), and explicitly notes that **zero-shot prompting of an off-the-shelf LLM
  becomes impractical for large window sizes** because the whole log sequence must go in the prompt.
  A separate approach (arXiv:2406.07467) needed an **ensemble + RAG** hybrid rather than a standalone
  LLM. *(Sound; LogLLM verified, others flagged.)*
- **The most on-point education result: GPT lost to a classical model on student log replays.**
  Maier & Baker (2025) prompted GPT-3.5/GPT-4 to code *gaming the system* from the **text replays**
  human coders use — and got **above-chance but weak** results (best κ ≈ 0.17), **beaten by a
  ported knowledge-engineered model (κ ≈ 0.26)**. Their diagnosis is the cleanest statement of the
  serialization problem we have: *"GPT's training data likely contains little that resembles text
  replays, making it difficult for GPT to interpret interaction data formatted as text, even though
  it is equally readable for humans."* *(Solid; read in full — see
  [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md).)*

### Why raw logs are hard for a pretrained LLM (the mechanism)

The reasons the "just prompt it" path has limits, and what to design around:

- **Serialization.** Events aren't language; they must be tokenized/serialized, and *how* you do it
  materially changes performance (the time-series result hinges on the encoding).
- **Long context.** A class session is thousands of events. Feeding the whole stream per inference is
  expensive and hits context limits — the LogLLM caveat. This pushes toward **summarizing /
  windowing / incremental memory** (which the spec's "detector memory" already anticipates).
- **Numeric reasoning & hallucination.** LLMs are shaky on precise counts/timings and can fabricate a
  plausible interpretation — dangerous for a detector meant to *trigger a human interview.*

## 3. Synthesis — what the evidence recommends

Laid against the spec's three detector types, the evidence gives a coherent, non-obvious answer:

| Question | Evidence-based answer |
|---|---|
| Switch wholesale to a from-scratch transformer? | **No** — at our data scale the KT record says the architecture alone won't reliably beat a simpler model; wins need domain-specific design. |
| Prompt a pretrained frontier LLM on raw logs? | **Yes, as the flexible starting point** — it's the only zero-labeled-data option, works for semantic/subjective "interestingness," and is already being published in education (ClickSight). Expect weaker precision and higher cost/latency than a specialist. |
| Train/fine-tune our own model? | **As an optimization**, once a pattern is (a) stable, (b) high-volume enough to matter for cost/latency, and (c) backed by enough labels — fine-tuned specialists then beat the prompted LLM. |

This is precisely the **"LLM first, ML/rules as optimizations"** progression the spec proposed —
now with an evidence base under it rather than an assumption. The hypothesis ("transformers > RNNs")
is *directionally* right but **most true where we have the least data-scale advantage**, and the more
actionable finding is about the **pretrained-vs-trained** axis, where "prototype by prompting, harden
by training" is well-supported.

## 4. Open questions we would be taking on

- **Does prompted-LLM detection reach usable precision on *our* logs?** ClickSight shows feasibility
  for *interpretation*; it does not establish reliable *detection* (low false-alarm rate) on K-12
  open-ended work like CLUE. This is genuinely unmeasured for our setting. *(Gap.)*
- **What's the right serialization of a CLUE/AP event stream for an LLM?** Raw log lines, the
  document-history diff, a rendered summary, or a hybrid? Untested. *(Gap.)*
- **Can we get enough labels to ever reach the "train a specialist" stage?** The KT lesson is that
  small data caps the payoff of fancy architectures; our labeling bottleneck (rare events, scarce
  observation time) is exactly the constraint DDCI itself is trying to relieve. *(Gap — and a
  chicken-and-egg worth naming.)*
- **Is there a foundation-model shortcut?** Self-supervised pretraining on *unlabeled* interaction
  logs (à la log-anomaly transformers) could give a reusable representation without per-construct
  labels. No education-specific version of this is established. *(Gap / opportunity.)*

---

## References

*Accessibility marked. Items I did not independently re-verify are noted and collected in
[papers-to-obtain.md](papers-to-obtain.md).*

- **Chen, Q., et al. (2019).** Behavior Sequence Transformer (**BST**). *DLP-KDD 2019*.
  arXiv:1905.06874. **Open access.** *(Verified.)*
- **Ghosh, A., Heffernan, N., & Lan, A. S. (2020).** Context-Aware Attentive Knowledge Tracing
  (**AKT**). *KDD 2020*. Author copy open (umass.edu). *(Verified.)*
- **Gruver, N., Finzi, M., Qiu, S., & Wilson, A. G. (2023).** Large Language Models Are Zero-Shot
  Time Series Forecasters. *NeurIPS 2023*. arXiv:2310.07820. **Open access.** *(Not re-verified.)*
- **Guan, W., Cao, J., Qian, S., Gao, J., & Ouyang, C. (2024).** LogLLM: Log-based Anomaly Detection
  Using Large Language Models. arXiv:2411.08561. **Open access.** *(Verified.)*
- **Jiang, Y., Bosch, N., Baker, R. S., Paquette, L., Ocumpaugh, J., Andres, J. M. A. L., Moore, A.
  L., & Biswas, G. (2018).** Expert Feature-Engineering vs. Deep Neural Networks: Which Is Better for
  Sensor-Free Affect Detection? *AIED 2018*. Open PDF (upenn.edu). *(Verified from primary text;
  feature-engineering beat DNNs on the single-threshold intervention decision.)*
- **Kang, W.-C., & McAuley, J. (2018).** Self-Attentive Sequential Recommendation (**SASRec**).
  *ICDM 2018*. arXiv:1808.09781. **Open access.** *(Verified.)*
- **Liu, Z., et al. (2022).** pyKT: Benchmarking Deep Learning Knowledge Tracing. *NeurIPS 2022
  D&B*. arXiv:2206.11460. **Open access.** *(Verified from abstract.)*
- **Pandey, S., & Karypis, G. (2019).** Self-Attentive Knowledge Tracing (**SAKT**). *EDM 2019*.
  arXiv:1907.06837. **Open access.** *(Verified.)*
- **Piech, C., et al. (2015).** Deep Knowledge Tracing (**DKT**). *NeurIPS 2015*. arXiv:1506.05908.
  **Open access.** *(Verified.)*
- **Radmehr, B., Shved, E., Güreş, F. B., Singla, A., & Käser, T. (2025).** ClickSight: Interpreting
  Student Clickstreams via LLMs. *AIED 2025*. arXiv:2505.15410. **Open access.** *(Verified.)*
- **(2024).** Fine-tuned small models vs. zero-shot LLMs for text classification. arXiv:2406.08660.
  **Open access.** *(Not re-verified — title/authors to confirm.)*
- **(2024).** Ensemble + RAG hybrid with a pretrained LLM for log anomaly detection.
  arXiv:2406.07467. **Open access.** *(Not re-verified — title/authors to confirm.)*
