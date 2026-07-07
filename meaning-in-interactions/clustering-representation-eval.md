# Evaluating Serializations by Clustering Their Embeddings

A method-focused companion to the other analytical docs. Where [techniques.md](techniques.md) and
[edtech-landscape.md](edtech-landscape.md) survey what's been *built*, this one examines one specific
**evaluation method** and asks whether it's real, established, and usable for us: **embed a
serialization of the data, cluster the embeddings without labels, and measure whether the discovered
clusters recover known groups.** The actionable version is item **3.8** in
[research-directions.md](research-directions.md); this doc is the evidence behind it.

The idea (the user's): to decide *which serialization of an interaction stream is best*, don't build
a prompt-plus-labeled-task eval for each candidate (that's the supervised
[3.1 serialization study](research-directions.md)). Instead, for each candidate serialization,
embed every session, cluster the embeddings, and score the clustering against a **known grouping** —
either **human labels** or the **groups an existing detector/DDCI pipeline already produces**. The
serialization whose embedding geometry best *recovers* those known groups is the one that most
exposes the data's structure to an LLM-style model. The same machinery doubles as a test of whether
an embedding/LLM can **match a classification ML model** (reproduce its groups) and — as an
extension — whether it **discovers structure the classifier missed**.

> **Honesty tiers (as in the other docs).** *Solid research* = canonical / repeatedly replicated.
> *Sound-but-unproven* = reasonable from one or few studies. *Gap* = barely touched, especially for
> K-12 open-ended classroom logs like CLUE. Every load-bearing citation below was fetched and checked
> (see the Verification note at the end); scan-only items are flagged inline.

---

## The one-paragraph verdict

**Every *component* of this method is established and canonical; the *specific composition* we'd run
is a genuine gap — which is exactly why it's worth doing.** Scoring an unsupervised clustering against
known labels has canonical metrics (V-measure, ARI, NMI, AMI). "Embed text → k-means → score against
gold labels with V-measure" is not a bespoke idea — it is the **operational definition of the MTEB
Clustering task**, i.e. representation-quality-via-clustering is a *first-class, benchmarked*
evaluation setting. That how-you-serialize-the-input changes the result is well documented (TabLLM;
prompt-format sensitivity; clustering quality depends heavily on the embedding). LLM-guided text
clustering exists (ClusterLLM), and "find categories beyond the labeled set" is a named research area
(Generalized/Novel Category Discovery). **What no verified source shows is anyone using this pipeline
to (a) *rank alternative serializations of the same data* or (b) do it on *learner-interaction logs
validated against human codes or detector groups.*** Both are open — the method is proven, the
application is not.

---

## 1. The scoring step is canonical — external cluster validation

Comparing a discovered clustering to a known partition ("do my clusters match the labels?") is a
solved measurement problem with primary sources. *Solid research.*

- **V-measure** — Rosenberg & Hirschberg, *EMNLP-CoNLL 2007*
  ([aclanthology.org/D07-1043](https://aclanthology.org/D07-1043/), open). Harmonic mean of
  **homogeneity** (h = 1 − H(C|K)/H(C), "each cluster contains only one class") and **completeness**
  (c = 1 − H(K|C)/H(K), "each class sits in one cluster"). This is the metric MTEB uses.
- **Adjusted Rand Index (ARI)** — Hubert & Arabie, *J. Classification* 1985
  ([doi 10.1007/BF01908075](https://link.springer.com/article/10.1007/BF01908075), Springer
  paywalled; PDFs mirrored). Chance-corrected agreement of two partitions, ~[−1, +1].
- **Normalized Mutual Information (NMI)** — Strehl & Ghosh, *JMLR* 2002
  ([jmlr.org/papers/volume3/strehl02a](https://www.jmlr.org/papers/volume3/strehl02a/strehl02a.pdf),
  open). NMI(X,Y) = I(X,Y)/√(H(X)H(Y)) ∈ [0,1], = 1 for identical partitions.
- **Adjusted Mutual Information (AMI)** — Vinh, Epps & Bailey, *JMLR* 2010
  ([jmlr.csail.mit.edu/papers/volume11/vinh10a](https://jmlr.csail.mit.edu/papers/volume11/vinh10a.pdf),
  open). Chance-corrected MI; "most often, such measures are used for **external validation**, that
  is, assessing the goodness of clustering solutions according to a *ground truth* clustering" —
  literally our use case.

**Two caveats that bite for us** (verified, from the same sources):

1. **V-measure is *not* invariant to dataset size or the number of clusters/classes** (the workflow
   explicitly refuted the opposite claim, 0–3). So you **cannot** naively compare raw V-measure
   across datasets of different sizes or different label-counts. For comparing serializations *on the
   same sessions with the same label set* this is fine; the moment counts differ, prefer the
   chance-corrected **AMI / ARI**.
2. **Un-adjusted NMI/MI have an inflated, non-constant chance baseline when N is small relative to
   the number of clusters** (Vinh et al.). Classroom studies routinely have few sessions and many
   candidate groups → **use AMI/ARI, not raw NMI**, whenever N is small or k varies.

---

## 2. The whole pipeline is a benchmarked setting — MTEB's Clustering track

The exact procedure — **embed the texts, run (mini-batch) k-means with k = number of gold labels,
score the clustering against those labels with V-measure** — is the definition of the **Clustering**
category of the **Massive Text Embedding Benchmark**. This is the strongest evidence that
"representation-quality-via-clustering" is an accepted evaluation, not something we'd be inventing.
*Solid research.*

- **MTEB** — Muennighoff, Tazi, Magne, Reimers; *EACL 2023*
  ([arXiv:2210.07316](https://arxiv.org/abs/2210.07316) /
  [aclanthology 2023.eacl-main.148](https://aclanthology.org/2023.eacl-main.148/), open). 8 task
  categories (Clustering is one); Clustering scored by "a mini-batch k-means model with batch size 32
  and k equal to the number of different labels," reported as V-measure. *(The "58 datasets / 112
  languages" figures are the original 2022 numbers.)*
- **MMTEB** — Enevoldsen et al.; 2025 ([arXiv:2502.13595](https://arxiv.org/abs/2502.13595), open).
  Much larger; confirms the recipe verbatim: "clustering is evaluated by computing the v-measure
  score (Rosenberg & Hirschberg, 2007) on text embeddings clustered using k-means." Ground truth =
  topical categories (scientific discipline, newsgroup).
- **Reused, not a one-off:** replicated for German
  ([arXiv:2401.02709](https://arxiv.org/html/2401.02709v1), open — "Like MTEB, we use Minibatch
  k-Means and V-measure") and Polish (PL-MTEB, [arXiv:2405.10138](https://arxiv.org/abs/2405.10138),
  scan-only).

**The important adaptation for us.** In MTEB the *input text* is fixed (a title, an abstract) and the
*embedding model* is the variable — it ranks embedders. **We'd invert it:** hold the embedder fixed
and make the **serialization** the variable, ranking serializations by how well their embeddings'
clusters recover our known groups. Same scoring machinery, independent variable swapped. No verified
source does exactly this swap — see §6.

---

## 3. Serialization *does* change the geometry — so the ranking is meaningful

The method only earns its keep if different serializations of the same data produce **different**
clusterings. They do. *Solid research* that format is a large lever; *sound-but-unproven* that it's
the dominant lever for our specific case.

- **TabLLM** — Hegselmann, Buendia, Lang, Agrawal, Jiang, Sontag; *AISTATS 2023*
  ([arXiv:2210.10723](https://arxiv.org/abs/2210.10723), open; code at clinicalml/TabLLM).
  Systematically compares **serialization methods** for rendering a tabular row into a
  natural-language string (hand-crafted templates, table-to-text models, LLM-generated). Finding:
  **Text Template best** in zero/few-shot; LLM serializations "showed mixed results" and hallucinated
  features; **format differences matter most in the low-data regime** and largely vanish by ~8
  examples. This is the closest published analog to "which serialization is best" — and it's on
  *tabular rows*, a reasonable stand-in for a per-event record.
- **Prompt-format sensitivity** — Sclar, Choi, Tsvetkov, Suhr; *ICLR 2024*
  ([arXiv:2310.11324](https://arxiv.org/abs/2310.11324), open). "Meaning-preserving" formatting
  changes swing accuracy by **up to 76 points** on LLaMA-2-13B (few-shot-specific). Direct evidence
  that trivial serialization choices are not neutral.
- **Embedding choice dominates clustering quality** — Petukhova, Matos-Carvalho, Fachada; *Int. J.
  Cognitive Computing in Engineering* 2025 ([arXiv:2403.15112](https://arxiv.org/abs/2403.15112),
  open). Clusters LLM text embeddings and scores them against ground truth with **external metrics —
  weighted F1, ARI, homogeneity** (plus internal silhouette / Calinski-Harabasz); GPT-3.5-Turbo
  embeddings win on 3 of 5 metrics on most datasets. Two lessons: (i) this *is* the
  representation-via-clustering eval, done with an LLM embedder; (ii) **the embedding model is a big
  confound** — the paper's own headline is that clustering quality "largely depends on the selection
  of textual embeddings." So we must **hold the embedder fixed** while varying serialization (or
  report the interaction). *The stronger "LLM embeddings recover structure better" reading was refuted
  in verification (1–2) as overreach — the metric enumeration and the 3-of-5 result stand; the causal
  "better representation" claim does not.*

---

## 4. LLM-in-the-loop clustering exists (but isn't what we'd run first)

If naive k-means-over-embeddings underperforms, there's a supervening line of work where an LLM
*guides* the clustering. Worth knowing, but note it's a different (heavier) method than the label-free
baseline we'd start with. *Solid research* that the method exists and is validated against labels.

- **ClusterLLM** — Zhang, Wang, Shang, McAuley, Xu; *EMNLP 2023*
  ([arXiv:2305.14871](https://arxiv.org/abs/2305.14871), open). Prompts ChatGPT with **hard triplet
  questions** ("does A correspond better to B than to C?") to elicit a clustering *perspective*, then
  fine-tunes a small embedder from that feedback — LLM-in-the-loop, not raw unsupervised k-means.
  Validated against known label sets. *(A related "Goal-Driven Explainable Clustering via Language
  Descriptions," EMNLP 2023, was named but not verified — treat as scan-only.)*

For our purpose, the plain **embed → k-means → external-validate** baseline is the right *first*
experiment (it's the MTEB recipe and it's cheap); ClusterLLM is the upgrade path if the baseline is
close-but-not-enough.

---

## 5. The "discover new structure" angle — a named field (secondary for us)

Per the scoping decision, our headline is **matching first** (can clustering reproduce known groups?);
"discovers something the classifier missed" is a *bonus*, not the pitch. But that bonus maps onto a
real, named research area, so it's not hand-waving. *Solid research* in vision; **domain mismatch flag:
both seminal papers are computer-vision, not logs or text.**

- **Generalized Category Discovery (GCD)** — Vaze, Han, Vedaldi, Zisserman; *CVPR 2022*
  ([arXiv:2201.02609](https://arxiv.org/abs/2201.02609), open; **verified directly**). Given a partly
  labelled set, categorize *all* unlabelled items where "the unlabelled images may come from labelled
  classes **or from novel ones**" — precisely the "known groups plus new ones" setting. Uses
  semi-supervised k-means and estimates the number of classes.
- **Novel Class Discovery (NCD)** — Han, Rebuffi, Ehrhardt, Vedaldi, Zisserman, *"Automatically
  Discovering and Learning New Visual Categories with Ranking Statistics," ICLR 2020*
  ([arXiv:2002.05714](https://arxiv.org/abs/2002.05714), open; **verified directly**); lineage traces
  to *Deep Transfer Clustering*, ICCV 2019 ([arXiv:1908.09884], scan-only). "Discovering novel classes
  in an image collection given labelled examples of other classes."

The translation to us: cluster the sessions; the clusters that **don't** line up with any existing
detector/human label are candidate *new* categories of student behavior. That's the "LLM finds
something DDCI's detectors couldn't" story — kept as an extension, gated behind a clean "it can match
first" result.

---

## 6. The gap — nobody (verified) has done this on interaction logs, or to rank serializations

This is where the novelty is, stated honestly. *Gap.*

- **No verified education / clickstream application of *this* pipeline.** The workflow's dedicated
  ed-tech search produced **no verified claim** of anyone clustering learner-interaction sequences (or
  their embeddings) and validating the clusters against **human codes or detector outputs**. The
  nearest neighbor is **ClickSight** (Radmehr et al., AIED 2025,
  [arXiv:2505.15410](https://arxiv.org/abs/2505.15410); deep-dive in
  [papers/clicksight-radmehr-2025.md](papers/clicksight-radmehr-2025.md)) — but that is an LLM
  *interpreting* clickstreams against theory-driven strategies, **not** embedding-cluster-vs-label
  validation. Different method. *(Stated as "not found in this pass," not "does not exist" — the
  search was not exhaustive; a targeted EDM/LAK sweep is the honest next step before claiming novelty
  in print.)*
- **No verified use of clustering-vs-labels to *rank serializations*.** Every MTEB-style result varies
  the *embedder* with the input fixed. Using external cluster validation with the **serialization as
  the independent variable** is the specific twist we'd contribute.

---

## 7. Is "separability ≈ LLM-readability" actually true? (partly studied)

The method's core assumption — that a serialization whose embedding *geometry* cleanly separates the
known groups is one an LLM will *read* well — is a **proxy**, and that proxy *has* been studied, though
not in this exact form. *Solid research* on the general principle; *gap* on the precise bridge.

- **The general principle is canonical: intrinsic geometry does not automatically predict downstream
  utility.** Schnabel, Labutov, Mimno & Joachims, *"Evaluation methods for unsupervised word
  embeddings," EMNLP 2015* ([aclanthology D15-1036](https://aclanthology.org/D15-1036/), open) is the
  seminal result — an embedding's **intrinsic** quality "did not necessarily correlate" with its
  **extrinsic/downstream** performance, and different evaluation schemes rank embeddings differently.
  "Good geometry" and "good on the task" can come apart. *(Paper identity verified directly; the
  finding is its well-known contribution, attested by multiple secondary sources — I did not read the
  full PDF this pass.)*
- **Probing says the same thing more sharply: decodable ≠ used.** Hewitt & Liang, *"Designing and
  Interpreting Probes with Control Tasks," EMNLP-IJCNLP 2019*
  ([arXiv:1909.03368](https://arxiv.org/abs/1909.03368) /
  [aclanthology D19-1275](https://aclanthology.org/D19-1275/), open; best-paper runner-up — verified
  directly). High probe accuracy can mean "the *probe* learned the task," not "the representation
  encodes/uses it"; their **control tasks / selectivity** separate the two. The analog for us: a
  serialization's embedding could *contain* the group structure (be clusterable) without a prompted
  LLM actually *using* it — and the reverse.
- **The specific "does embedding geometry predict task performance" question is being tested right now —
  and the news is mixed-to-encouraging.** Myntti, Kanerva, Laippala & Ginter, *"Structure Retention in
  Embedding Spaces as a Predictor of Benchmark Performance,"* 2026
  ([arXiv:2605.22202](https://arxiv.org/abs/2605.22202), **scan-only, recent — verified title/authors/
  finding via abstract only**) test **25 embedding models** on 5 MTEB tasks and find geometric
  structure-retention measures (nearest-neighbor overlap, ICA-magnitude differences between paired
  instances) **correlate up to ~0.97 with task performance**, concluding "high-performing embedding
  models organize their embedding spaces in a consistent way." A direct, *positive* answer to "can
  geometry predict performance" — but note it predicts an *embedding model's own* benchmark tasks
  (retrieval, bitext, pair-classification), **not** a *generative* LLM's reading of the same text.
- **The exact cross-pathway bridge — embedding separability predicting what a *prompted generative* LLM
  does with the same serialization — is essentially untested.** An embedding encoder and a generative
  decoder-reading-a-prompt are *different pathways* even within one model family, which is precisely
  where the proxy can slip. The nearest evidence is that LLMs can hold good in-context representations
  yet fail to *use* them when generating (*"Language Models Struggle to Use Representations Learned
  In-Context,"* [arXiv:2602.04212](https://arxiv.org/abs/2602.04212), **scan-only, not verified**) —
  the decodable-≠-used gap, one level up.

**Honest status of the claim.** The *general* "geometry is a proxy, not identical to task utility" is
well-established (Schnabel 2015; Hewitt & Liang 2019). "Geometry can *strongly* predict an *embedding
model's* task performance" now has direct positive evidence (Myntti et al. 2026). The *specific* leap
we'd make — embedding-cluster separability as a stand-in for *generative-LLM* readability of a
serialization — is the untested part, and the reason §9 keeps this a **shortlisting** tool feeding the
supervised [3.1](research-directions.md) rather than a replacement for it. If we ran 3.8 *and* 3.1 on
the same serializations, we'd produce exactly the missing datapoint: does clustering-separability rank
serializations the same way a prompted LLM's task performance does?

---

## 8. What the method can and can't tell you (read before trusting a score)

- **It measures geometric separability, which is a *proxy* for LLM-readability — not identical to it**
  (§7 examines how well-founded that proxy is).
  A serialization could place the known groups in tidy embedding clusters yet a *prompted* LLM might
  still read a different serialization better for a downstream task (and vice versa). So a
  clustering-based ranking is a **cheap, label-light pre-filter** over serializations, not a
  replacement for the supervised task eval in [3.1](research-directions.md). Best used to *shortlist*
  serializations before paying for prompt-and-label evaluation.
- **The embedder is a confound (§3).** Hold it fixed across serializations, or report the
  serialization×embedder interaction. Pick an embedder "similar to what the LLM is based on" (the
  user's instinct) — but know the result is conditional on that choice.
- **"Match the ML model" ≠ "match the truth."** Reproducing a *detector's* groups only shows the
  embedding can recover the **detector's decisions**, which are themselves imperfect against human
  labels. So run **both** ground truths: human labels are the real target (matching them is the
  finding); detector groups are a secondary "can we reproduce the existing pipeline cheaply" check.
- **Chance-correct with small N.** Classroom session counts are small and candidate group-counts can
  be large → AMI/ARI, not raw V-measure/NMI, for anything cross-dataset or variable-k (§1).
- **Domain mismatch, as always.** Every verified result here is on **text documents, tabular rows, or
  images** — none on open-ended K-12 interaction logs. The metrics and the MTEB recipe transfer
  cleanly (they're data-agnostic); whether *our* serializations of *our* messy document-event streams
  cluster usefully is exactly the untested question.

---

## 9. How it maps onto our case (the concrete experiment)

1. Fix an embedding model (ideally close to the LLM family we'd deploy).
2. Pick a labeled slice of sessions with **two** ground-truths available: **human labels** (affect /
   behavior / "interesting moment," via text-replay coding — [3.3](research-directions.md)) and
   **DDCI/detector groups** for the same sessions.
3. For each candidate **serialization** (raw log lines, document-history diff, natural-language
   rendering, text-replay format, hybrid — the [3.1](research-directions.md) menu): embed every
   session → mini-batch k-means at k = number of known groups → score with **AMI/ARI (and V-measure)**
   against each ground-truth.
4. **Rank** serializations by recovery of the human labels (primary) and detector groups (secondary).
5. *(Extension, gated on a clean match)* inspect clusters that align with **no** known group as
   candidate novel behavior categories (§5).

**Relationship to the other directions.** This is the **label-light sibling of
[3.1](research-directions.md)**: 3.1 asks "which serialization does an LLM *perform* best on" (needs a
prompt + a labeled task per serialization); this asks "which serialization's geometry best *separates*
the known groups" (needs only the labels, no prompt, no task-tuning). Cheaper to run, weaker as
evidence — a shortlisting tool feeding 3.1. It shares the **compact-and-query / serialization**
through-line (working-notes) and the labels it needs come from [3.3](research-directions.md).

---

## 10. Dual use: the same embeddings can seed a vector database

The embed-a-serialization work pays off **twice**. Once a serialization is chosen and every session
embedded, those embeddings aren't only for the one-off clustering evaluation in this doc — they're
also the **index for a vector database** over the whole log corpus, enabling *"find me sessions with a
pattern like this one"* similarity search across a large body of interaction logs. That's the
retrieval side of the **compact-and-query** through-line (working-notes): the same pipeline both
*evaluates/chooses* the serialization (this doc) and *retrieves* similar sessions at scale.

**A serialization that clusters well is a strong candidate for the vector-DB representation** — if the
embedding geometry cleanly separates known groups, nearest-neighbor lookups in that space tend to
return genuinely similar sessions. And for *this* use the §7 proxy worry is **weaker, not stronger**:
a vector DB uses the embedding geometry *directly* (retrieval *is* a geometric task), so "separability
predicts utility" stands on firmer ground here than in the generative-reading case — it is exactly
what MTEB's *Retrieval* category and the structure-retention result (Myntti et al., §7) measure.

**But the caveat cuts the other way on embedder choice — the vector-DB case has *more* freedom, so
exploit it.** For clustering-as-serialization-eval we deliberately fix the embedder "close to the
deploy LLM family" (§3, §8), because the point is to predict what *that* LLM will read. The vector DB
has no such constraint: nothing downstream reads the raw embedding as a prompt, so we're free to pick
the **best retrieval embedder available**, whatever its family — and should try several. Two
consequences: (1) the serialization×embedder winner for *clustering* is a good starting point but
**not automatically** the best for *retrieval* — clustering and retrieval are distinct MTEB tasks, and
a representation strong at one can be middling at the other (the no-universal-embedding finding, §2/§7);
(2) so the honest plan is to **reuse the pipeline but re-run the embedder sweep** for the vector-DB
use, treating the clustering result as a prior, not a verdict.

---

## Verification note

- **Directly verified by me (fetched this session):** GCD (Vaze et al., CVPR 2022, arXiv:2201.02609 —
  title, authors, task definition, quote confirmed); NCD/RankStats (Han et al., ICLR 2020,
  arXiv:2002.05714 — title, authors, venue, problem statement confirmed); Hewitt & Liang (EMNLP-IJCNLP
  2019, D19-1275 / arXiv:1909.03368 — title, authors, control-tasks/selectivity argument confirmed);
  Schnabel et al. (EMNLP 2015, D15-1036 — identity confirmed; the intrinsic≠extrinsic finding is its
  known contribution, taken at abstract/secondary level, full PDF not read); Structure Retention
  (Myntti et al., 2026, arXiv:2605.22202 — title, authors, 25-model scope, ~0.97 correlation finding
  confirmed via abstract).
- **Verified 3–0 by the deep-research workflow's adversarial pass:** V-measure (D07-1043), ARI
  (BF01908075), NMI (strehl02a), AMI (vinh10a), MTEB (2210.07316 / eacl-main.148), MMTEB (2502.13595),
  German MTEB replication (2401.02709), TabLLM (2210.10723), Sclar prompt-format (2310.11324),
  ClusterLLM (2305.14871). Petukhova (2403.15112) metric-enumeration verified 2–1; its stronger
  "better representation" claim was **refuted (1–2)** and is *not* asserted here.
- **Explicitly refuted in verification (do not reintroduce):** "V-measure is invariant to N / #clusters
  / algorithm" (0–3) — hence the §1 caveat.
- **Scan-only (named but not independently re-verified — flag before leaning hard):** PL-MTEB
  (2405.10138), Deep Transfer Clustering (1908.09884), "Goal-Driven Explainable Clustering" (EMNLP
  2023), "Language Models Struggle to Use Representations Learned In-Context" (2602.04212). ClickSight
  (2505.15410) is verified elsewhere in this repo. *Note: 2602.xxxxx / 2605.xxxxx are 2026 arXiv IDs —
  very recent; re-verify before print.*
- **Coverage gap (honest):** the ed-tech/clickstream application of this exact pipeline and the
  serialization-ranking use both produced **no verified prior work** — treated as a novelty
  opportunity in §6, not a proven absence. A targeted EDM/LAK search should precede any published
  novelty claim (cf. the [[verify-absence-claims]] rule).
