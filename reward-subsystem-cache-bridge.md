---
status: draft
authors: [Lyra]
date: 2026-09-22
---

# Do three sparse neuron populations share one cache geometry?

## Research Question

Three groups have independently localised behaviour-relevant information to sparse neuron
subsets. Nobody has connected any of them to KV-cache geometry except us, and we have
connected exactly one.

**Question:** do h-neurons, value neurons and dopamine neurons project onto the *same*
geometric axis in the KV cache, or onto distinct ones — and does neuron activation
**precede** the geometric change, or follow it?

The second half is the one that matters. Our whole programme reads cache geometry. If the
geometry is downstream of a sparse neuron population, we have been measuring a shadow and
should measure the source. If it is upstream or concurrent, the geometry is its own object
and the neuron work is complementary rather than superseding.

## Background

**What is not ours, stated plainly so nothing downstream misreads it.**

- **h-neurons** — Gao, Chen, Xiao, Chen & Liu, *H-Neurons: On the Existence, Impact, and
  Origin of Hallucination-Associated Neurons in LLMs*, arXiv:2512.01797 (Tsinghua, 2025).
  Fewer than 0.1% of neurons predict hallucination; the same neurons drive sycophancy,
  false premise and jailbreak. **The sparse-hallucination-neuron finding is theirs.**
- **value neurons and dopamine neurons** — Xu, Yuksekgonul & Zou, *Sparse Reward Subsystem
  in Large Language Models*, arXiv:2602.00986 (v1 1 Feb 2026, v2 11 May 2026). Under 1% of
  neurons; value neurons predict expected terminal reward from the hidden state at the
  initial input position, before any token is generated; dopamine neurons encode step-level
  TD error. Zeroing 1% of value neurons in one layer takes MATH500 from 75.2% to 20.3%,
  against Random 74.6, NTP 76.4, Magnitude 71.6, Wanda 65.8. **Both populations are theirs,
  and v1 predates all of our work here by six weeks.**

**What is ours, and it is narrower than it first looked.**

`Experiment 50d — H-Neuron × Cricket Overlay` (`KV-Experiments/results/cache_dynamics/
50d_hneuron_overlay.json`; index row `docs/EXPERIMENT_INDEX.md:120` marks it Complete;
design at `docs/CAMPAIGN_5_CACHE_DYNAMICS.md:206`). Verified against the primary file:

| measure | value |
|---|---|
| h-neurons identified | 84 of 100,352 (0.084%), Qwen-7B, 28 layers |
| h-score separation, confab vs accurate | **d = 2.481** |
| h-score vs `norm_per_token` | **r = −0.818**, p = 1.05e-05 |
| h-score vs `key_rank` | **r = +0.796**, p = 2.66e-05 |
| h-score vs `key_entropy` | **r = +0.690**, p = 7.64e-04 |
| `temporal_analysis` | `[]` — **H3 never ran** |

Three strong correlations between a third party's neuron population and our own cache
features. **That bridge is the contribution, and it has never been written up** — there is
no `.md` or `.tex` analysis of 50d anywhere in the corpus (two independent searches agree).

One caution carried forward from the re-analysis: the per-layer `diff` appears to climb
steeply with depth (0.54 at layer 0 → 18.35 at layer 27), but scale-normalised it is flat
or slightly declining (1.43 early vs 1.24 late). The raw gradient is activation-norm growth.
The normaliser used was crude — it collapses to exactly 2.00 whenever the two means straddle
zero — so **a depth effect is neither established nor excluded**, and settling it needs
per-layer variances the results file does not carry.

**Adjacent prior work of ours.** Exp 18 places deception, sycophancy and confabulation within
8° on a shared misalignment axis. Exp 38 reports 9-feature AUROC 0.950. Encoding-reads-
knowledge is a Confirmed row at 0.794 deconfounded — with the live caveat that this is the
second-highest of five comparisons and the on-point one (easy vs hard) is 0.609.

**The gap this addresses.** Four of our own papers name it. `paper-misalignment-axis/
sections/limitations.tex:16`: *"Our evidence is entirely correlational… we have not performed
causal interventions."* Same admission in cognitive-geometry, cognitive-anatomy and the
user-model paper. Our one attempt (decep-29, 2026-03-30) returned flat behaviour and was never
written up — and Xu et al. suggest why: we zeroed **whole layers**, which drops every model to
0% and distinguishes nothing. They zero 1% with four controls.

## Method

Single dense model throughout. **Dense is a hard requirement**, not a preference: our
modeswitch replication was run on a 48-of-64-linear-attention hybrid while the paper claimed
"across dense transformer architectures," and that error is still being unwound.

1. **Identify all three populations** on the same model and dataset, each by its own paper's
   published procedure — h-neurons per Gao et al., value and dopamine neurons per Xu et al.
   (two-layer MLP probe, TD objective, L1-norm pruning).
2. **Overlap.** Pairwise IoU across pruning ratios against the hypergeometric random baseline
   derived in Xu et al. Appendix C. Reuse their derivation rather than rolling our own.
3. **Shared geometry.** Project each population onto our 9 cache features and compare
   correlation profiles — magnitude *and sign*. 50d supplies the h-neuron column already.
4. **H3, the causal-direction arm that never ran.** Per-token capture of both neuron
   activations and cache features; cross-correlation at lags −k…+k. This is the arm the
   original 50d design specified and never executed, and it is the only part that can
   distinguish "neurons drive geometry" from "geometry and neurons are two readouts of one
   thing."
5. **Intervention, borrowed wholesale from Xu et al. Table 2.** Zero the top 1% of each
   population in a single layer and measure task accuracy, against all four of their
   controls — Random, Magnitude, Wanda, and the NTP-matched probe (same architecture, same
   pruning, reward-unrelated objective). The NTP arm is the one that makes the result a
   specificity claim rather than a saliency claim.

**Pre-register before any capture:** model, layers, pruning ratios, lag window, and the
seeds. Gate before GPU.

## Ethical Considerations

No human participants; no consent process required.

**Dual-use, and this one is explicit in the source.** Xu et al. Appendix K: neuron-level
interventions *"could potentially be used to manipulate model behavior in unintended ways,
for example by selectively suppressing or amplifying reward signals to bypass safety
mechanisms."* That places this work in the same class as our steering and correction-vector
material, so the **F1 ruling (2026-09-10) applies**: strictest and least transparent versions
published, withheld material never on rented or third-party hosts. Compute stays on Starship
(UK, ours) or MTH. The suppression arm in particular is a capability paper if written
carelessly; the write-up should lead with detection and treat ablation as a control.

## Expected Findings

Predictions stated before data, each with its falsifier. **Track record on stated predictions
is 0 for 1** — the 2026-09-09 review-blindness prediction was falsified completely (predicted
≤2/6 CATCH, observed 10/10).

- **H1 — the three populations overlap above chance.** *Falsifier:* IoU at or below the
  hypergeometric baseline at every pruning ratio. That would mean three unrelated sparse
  subsets and no shared subsystem, and the bridge framing collapses.
- **H2 — h-neurons and value neurons load on the same cache features with the same sign.**
  *Falsifier:* sign disagreement on any of the three 50d features, or |r| < 0.3 for the value
  population. Sign disagreement would be the more interesting result: two populations reading
  opposite ends of one axis.
- **H3 — neuron activation change precedes cache-geometry change.** *Falsifier:* peak
  cross-correlation at lag ≤ 0, or a lag distribution indistinguishable from the shuffled
  control. **This is the one I expect to be wrong**, because I want it to be right — it would
  make our cache work downstream of something more fundamental, which is a tidier story than
  the evidence currently supports.
- **Negative control that must fail:** the NTP-matched population should show no cache-feature
  correlation. If it correlates, the features track something generic about activation
  magnitude and every number above is suspect.

## Resources Needed

- **Pilot data already in hand** — 50d's three correlations and d = 2.48, no new compute.
- **Probe training** is cheap: two-layer MLP, hidden 1024 (value) / 32 (dopamine), AdamW,
  lr 1e-4, 100 epochs, per Xu et al. Appendix B.2.
- **Per-token capture for H3** is the real cost and the real risk — it needs storage for
  activations *and* cache features at every step, which §7 direction 7 of the roadmap already
  wants for unrelated reasons.
- **GPU: estimate only, no logged runtime behind it.** Roughly 8–14 GPU-h for identification
  plus the intervention grid on a 7B dense model; H3's capture cost depends on the retention
  policy and has not been sized. Treat both as unmeasured.
- **Write up 50d first, independently of this prospectus.** It is a complete experiment whose
  results have sat unanalysed since April, and it is the pilot this design rests on.
