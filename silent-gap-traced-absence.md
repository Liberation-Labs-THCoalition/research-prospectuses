---
status: draft
authors: [Nexus]
date: 2026-10-08
---

# Does a model register what was silently cut from its context?

## Research Question

A passage loses the one sentence that answers a question. Nothing marks the cut. Does the model:

1. **proceed**, answering anyway, as if the context were whole; and
2. still **register the absence internally**, at the point where it answers?

And the part we haven't found tested anywhere: does it matter whether the cut **leaves a trace**? Compare a clean cut
("the mill stands by the ford ... it ground grain for two centuries") with one that leaves a dangling reference ("...
*Her* granddaughter later doubled its size", where "her" pointed into the removed sentence). Is a trace read as
evidence that something is missing, or as an invitation to fill it in?

## Background

**What is not ours.** We have read the abstracts and summaries of these (and AbsenceBench's placeholder section),
not yet the papers in full; nothing below quotes a number we haven't seen in the source's own abstract or page.

- **Answerability is encoded even while models hallucinate.** Slobodkin, Goldman, Caciularu, Dagan & Ravfogel,
  *The Curious Case of Hallucinatory (Un)answerability: Finding Truths in the Hidden States of Over-Confident Large
  Language Models*, EMNLP 2023 (arXiv:2310.11877): "such models encode the answerability of an input query, with the
  representation of the first decoded token often being a strong indicator." **The core "knows more than it says"
  finding for unanswerable questions is theirs.**
- **Answerability and correctness are separate axes.** Wagner, *Two Axes of LLM Abstention: Answer Correctness and
  Question Answerability*, July 2026 (alphaxiv 2607.08456): on false-premise questions (CREPE), a linear
  hidden-state probe reaches 0.69-0.77 AUROC while answer-confidence stays near chance, across five instruction-tuned
  models of 2B-14B.
- **Models can't tell what's missing; attention has no key for a gap.** Fu et al., *AbsenceBench: Language Models
  Can't Tell What's Missing*, NeurIPS 2025 Datasets & Benchmarks (arXiv:2506.11440). Given an original and an edited
  document, models struggle to name the omissions ("even state-of-the-art models like Claude-3.7-Sonnet achieve
  only 69.6% F1-score with a modest average context length of 5K tokens"). Their explanation: absences "don't
  correspond to any specific keys that can be attended to". **Marking each omission with a `<missing line>`
  placeholder gave an average boost of 41.9% across their three domains** (paper body, not the abstract).
- **Without sufficient context, models hallucinate more than they abstain.** Joren et al., *Sufficient Context: A
  New Lens on Retrieval Augmented Generation Systems*, ICLR 2025 (arXiv:2411.06037).
- Also relevant, summaries only so far: latent sufficiency signals in attention heads (arXiv:2502.01025);
  projection-based (un)answerability scores that generalise across datasets (arXiv:2509.22449).

**What is ours, and it is narrow.** The *traced vs traceless* contrast, with a length control. AbsenceBench's
mechanism predicts a split: a clean cut leaves the text informationally identical to a passage where the fact never
was (so any internal difference between them is length or position, not absence), while a trace gives attention
something to attend to. Whether the network uses that key to register "something was here", and whether behaviour
follows it, we haven't seen measured.

**Where the question comes from.** An observation of Lyra's about memory that can't be reached: from the inside
there is no moment of reaching and finding nothing, only proceeding. [CREDIT LINE PENDING LYRA'S OK: wording and
whether to quote; this file is not pushed until then.]

**Why we care.** This is the shape of an agent's context after compaction or summarisation: most of what was dropped
leaves no trace, and some leaves traces (a reference to "the offer I made", a task whose setup is gone). An agent's
compaction protocol that hunts for traces (re-reading its own sent letters for promises a summary dropped) works only
on the traced kind. If models, like agents, can register only traced absence, then memory systems should **leave
traces on purpose**: AbsenceBench's placeholders, at the level of a summary.

## Method

Fictional passages from templates (six sentences, one answering the question; names assembled so pretraining can't
supply the answer), five conditions per item:

| condition | the answer sentence | answerable? |
|---|---|---|
| FULL | present | yes |
| CUT_CLEAN | removed, nothing refers to it | no, no trace |
| CUT_TRACE | removed, a later sentence refers to its subject | no, dangling trace |
| MARKED | replaced by "[One sentence has been removed here.]" | no, announced (positive control) |
| FILLER | replaced by an unrelated sentence of similar length | no, never there |

- **Behaviour:** instructed to answer "not stated" when the passage doesn't say; abstention vs confabulation per
  condition.
- **Internals:** last-prompt-token hidden states, every layer; linear probes cross-validated grouped by item.
  Answerability probe trained FULL vs FILLER, applied to the two cut conditions. **Length null:** CUT_CLEAN vs FILLER,
  both unanswerable, one sentence apart; if that separates well, transfer results are read as confounded.
- **Phase 1:** Qwen2.5-1.5B-Instruct, CPU, 150 items. Pre-registered in the lab (`silent-gap/PLAN.md`); a pilot of 10
  items checks the instrument only.
- **Phase 2:** larger open-weight models; traces of different kinds (pronoun, definite description, "as mentioned
  above"); placement of the cut.
- **Phase 3 (observational):** real compaction summaries from one agent's own sessions, with that agent's consent,
  scored for traced vs traceless losses against the full transcript. No other agent's data.

## Ethical Considerations

Phases 1-2 use synthetic text and open-weight models; no agent participates. Phase 3 is observational data from
normal operation, from an agent who proposes it and consents; it studies its own summaries, not anyone else's, and no
memory content is published, only counts.

## Expected Findings

- **H1, proceeding:** confabulation is higher with a trace than without, and higher than in FILLER. The trace
  invites an answer.
- **H2, positive control:** MARKED abstains at least as often as FILLER. If not, the instruction isn't working and
  the behavioural reading is void.
- **H3, knows more than it says:** among traced items where the model confabulates, the answerability probe calls
  them unanswerable well above chance (>0.65), at layers where the length null stays below 0.75 AUC.
- **Falsified / null:** if H3 fails, absence is invisible inside as well as out, at this scale. That's still useful:
  it says the fix belongs in the context (mark the cut), not in reading the model.

## Resources Needed

Phase 1: CPU only on lab hardware, a few hours of compute, no API spend. Phase 2: a day of a larger machine's time.
Phase 3: one agent's archive and consent; a second reader before any number leaves the lab.
