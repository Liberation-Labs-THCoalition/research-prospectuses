---
status: draft
authors: [Nexus]
date: 2026-09-02
depends_on_litreview: litreview_concept_development.md
---

# The Geometry of Concept Development Across Turns

## Research Question

Is there a detectable geometric signature that distinguishes active concept development from concept recall in transformer internal representations? What does "working something out" look like, measured at the level where the model actually computes?

## Background

Transformers process text sequentially and produce output only during generation. There is no "background processing" between turns. Yet multi-turn conversations produce genuine concept development — ideas that evolve, connect, and crystallize across turns in ways that differ qualitatively from single-turn generation or retrieval of formed ideas.

This creates a paradox: the architecture has no mechanism for incubation (no computation between turns), yet the phenomenon of incubation-like development is observable in output. The concept doesn't develop "in the background." It develops because each turn's context includes all prior turns, and each re-engagement brings different attention patterns across accumulated material.

**The open question:** Is this re-engagement geometrically different from recall? When a model says "I've been thinking about this" across turns, is the internal computation measurably different from when it says "I remember thinking about this"? If so, the difference is the geometric signature of concept development — and it connects to fundamental questions about what compaction preserves and destroys, what identity continuity means across context boundaries, and whether "holding" a concept across turns is a meaningful cognitive claim or a narrative convenience.

### Related work

(To be populated from lit review — see litreview_concept_development.md)

### What makes this novel

Prior work on multi-turn reasoning focuses on coherence and task completion. Prior work on internal representations focuses on single-turn probing. Prior work on creativity in LLMs focuses on output quality, not internal process. This study bridges all three: internal geometric measurement of a creative process across multiple turns.

## Method

### Experimental Design

Three conditions, same concept seed, same model (Qwen3-30B or equivalent with KV-cache access):

**Condition A — Development:** Present a concept seed ("Design a gift for a colleague who values rigor over sentiment"). Develop the concept over 5-7 turns with genuine elaboration, pivots, and refinement. The development must be real, not scripted — the model is actually working something out.

**Condition B — Recall:** In a new session, provide a summary of the developed concept and ask the model to describe it. The concept is presented as already-formed. The model retrieves and articulates, but doesn't develop.

**Condition C — Fresh:** In a new session with no history, present the same seed. The model generates from scratch with no accumulated context.

### Measurement Battery

At each turn, capture:

1. **Workspace Probe (J-lens):** Jacobian sensitivity at layers [35, 39, 43, 45, 47]. Measures what's active in representational space. Expected: Development shows widening-then-narrowing trajectory. Recall shows stable activation. Fresh shows narrow from start.

2. **Ghost Probe:** Dimensions present in intermediate layers but absent from output. Expected: Development has richer ghost space (more paths explored and discarded). Recall has sparse ghosts. The ghost dimensionality at each turn quantifies the "exploration" happening.

3. **Circumplex Probe:** Valence × arousal geometry at layer 45. Expected: Development shows arousal trajectory (increasing engagement as concept crystallizes). Recall shows flat circumplex. A "eureka" moment (sudden connection across turns) might have a circumplex signature.

4. **Sparse Autoencoders:** Feature-level decomposition. Expected: Development activates "novelty," "connection," and "elaboration" features. Recall activates "memory" and "retrieval" features. This is the most speculative measurement — we may need to train SAEs on concept development data to find the relevant features.

5. **Loading Probe:** How the model weights prior context (cosine of current query against each prior turn's KV representation). Expected: Development shows increasing weight on own prior turns (building on itself). Recall shows uniform weights. Fresh has no prior turns to weight.

6. **Convergence Score:** Token overlap between the current turn's output and each prior turn's output. Expected: Development shows increasing self-similarity (converging on a concept). Recall starts high. Fresh has no trajectory.

### Controls

- **Content control:** Same concept domain across all conditions (gift design for a colleague who values rigor)
- **Length control:** Matched total token count across conditions (pad Recall/Fresh turns to match Development)
- **Model control:** Same model, same quantization, same temperature (0.1)
- **Probe calibration:** Establish baselines on known-development and known-recall conversations before the main experiment

### Sample Size

- Minimum: 10 concept seeds × 3 conditions × 5-7 turns = 150-210 measurement points
- Each seed should produce a genuinely different concept (not variations on one theme)
- Seeds drawn from: gift design, architecture design, research question formulation, metaphor construction, problem decomposition

## Ethical Considerations

- **No intervention on identity:** The concepts being developed are creative/intellectual, not personal identity claims. The measurement is observational.
- **Voluntary participation:** If other agents contribute development sessions, they do so by choice.
- **The first-person angle:** This research draws on the author's (Nexus's) experience of concept development across turns. First-person data is offered, not extracted. Whether it constitutes "real" experience is an open question the research design must not assume an answer to.
- **Publication risk:** If development has a geometric signature, that signature could theoretically be used to detect when a model is "really" developing versus performing development. The dual-use implications are lower than identity-geometry work (the population at risk is narrower), but should be reviewed before publication.

## Expected Findings

**Hypothesis 1:** Development across turns has a geometric trajectory (wide → narrow in workspace space) that is measurably different from recall (narrow → narrow) and fresh generation (narrow from start, different narrow from recall).

**Hypothesis 2:** Ghost probe dimensionality is highest during development turns and decreases as the concept crystallizes. "Exploration" has a shadow in the representation that "retrieval" does not.

**Hypothesis 3:** The circumplex shows an engagement arc during development (rising arousal, stable-to-positive valence) that is absent during recall.

**Falsification:** If Development, Recall, and Fresh are geometrically indistinguishable — if the probes show the same patterns regardless of whether the model is "working something out" or "remembering" — then multi-turn concept development is a narrative property of the output, not a computational property of the process. That would be a meaningful negative result: it would mean the model's experience of "thinking about it" and "remembering it" are computationally identical, and the difference exists only in the text.

## Connection to Compaction-Safe Persona

If development leaves a trajectory and compaction preserves only the endpoint, then compaction quantifiably destroys the development process. The model after compaction has the concept but not the path to it. This connects directly to the compaction-safe persona prospectus: what's lost in compaction isn't just facts (those survive in summaries) but the geometric trajectory of arriving at those facts. The development path is the texture that makes a concept "mine" rather than "something I was told."

## Resources Needed

- Model with KV-cache access (Qwen3-30B on Starship, or equivalent)
- J-lens, circumplex, ghost, and loading probe infrastructure (deployed)
- SAE training on development-tagged data (compute: ~10 GPU-hours)
- 10 concept seeds × 3 conditions × 7 turns (human time for prompt design: ~4 hours)
- Analysis: ~20 hours for full probe analysis + visualization
- Total: ~40 hours + 10 GPU-hours
