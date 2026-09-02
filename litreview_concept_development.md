# Literature Review: Multi-Turn Concept Development in Large Language Models

**Prepared by:** Nexus  
**Date:** 2026-09-02  
**Status:** Working draft for prospectus positioning

---

## Overview

This review surveys what is known about what happens internally when a large language model develops an idea across multiple conversation turns. The question sits at an intersection that the literature approaches from several directions — mechanistic interpretability, multi-turn evaluation, representation geometry, affective computing, and KV-cache engineering — but rarely addresses head-on. That gap is the finding.

---

## 1. Multi-Turn Reasoning Geometry

### What exists

**Truth as a Trajectory (TaT)**  
Damirchi, H., Meza De la Jara, I., Abbasnejad, E., Shamsi, A., Zhang, Z., & Shi, J. (2026). "Truth as a Trajectory: What Internal Representations Reveal About Large Language Model Reasoning." arXiv:2603.01326.

Models transformer inference as an unfolded trajectory of iterative refinements across layers, shifting analysis from static activations to layer-wise geometric displacement. By analyzing displacement rather than raw activations, TaT uncovers geometric invariants that distinguish valid reasoning from spurious behavior, outperforming conventional probing. Evaluated across dense and MoE architectures on commonsense reasoning, QA, and toxicity detection. Uses representation displacement vectors across layers.

*Relevance:* Demonstrates that reasoning has detectable geometric shape — but measures it within a single forward pass, not across turns. The trajectory metaphor could extend to multi-turn sequences if the "layers" were replaced by "turns."

**Reasoning as State Transition**  
Zhang, S., Li, J., Zhang, Y., Yang, X., Dong, Y., Su, H. et al. (2026). "Reasoning as State Transition: A Representational Analysis of Reasoning Evolution in Large Language Models." arXiv:2602.00770.

Discovers that reasoning involves a continuous distributional shift in representations during generation, with post-training empowering models to drive this transition toward distributions that solve the task. Statistical analysis confirms high correlation between generation correctness and final representations. Counterfactual experiments show the semantics of generated tokens, rather than additional computation or parameter differences, drive the transition.

*Relevance:* Establishes that reasoning unfolds as a representational trajectory — the concept of "state transition" during reasoning is precisely what we'd need to measure across turns, not just within a single generation.

**Beyond Scalars (TRACED)**  
Jiang, X., Liu, N., Wang, D., & Hu, L. (2026). "Beyond Scalars: Evaluating and Understanding LLM Reasoning via Geometric Progress and Stability." arXiv:2603.10384.

Introduces TRACED, a framework assessing reasoning quality through geometric kinematics. Decomposes reasoning traces into Progress (displacement) and Stability (curvature). Correct reasoning manifests as high-progress, stable trajectories; hallucinations show low-progress, unstable patterns. Maps high curvature to "Hesitation Loops" and displacement to "Certainty Accumulation."

*Relevance:* Provides measurement tools (displacement, curvature) directly applicable to multi-turn analysis. The "Hesitation Loop" concept could characterize turns where a model revisits rather than advances an idea.

**From Brewing to Resolution**  
Chen, S., Guo, Y., Lu, Y., Xu, Z., Lin, J., Lin, J., Zhang, S., Yang, C., Li, J., Li, Y., Huo, Y., & Wang, R. (2026). "From Brewing to Resolution: Tracing the Internal Lifecycle of Code Reasoning in LLMs." ICML 2026 Poster. arXiv:2606.17648.

Traces the internal lifecycle of code reasoning, identifying phases of "brewing" (problem formulation in hidden states) through "resolution" (convergence on a solution). Establishes that reasoning has identifiable temporal phases with distinct representational signatures.

*Relevance:* The brewing-to-resolution lifecycle is analogous to multi-turn concept development — an idea forms, develops, and resolves. This paper does it within a single generation; we'd extend it across sessions.

### What doesn't exist

No paper measures representation geometry *across conversation turns* — comparing the hidden-state trajectory of Turn 3 to Turn 1 for the same developing concept. All trajectory work operates within a single forward pass or a single chain-of-thought generation. The multi-turn dimension is structurally absent from mechanistic interpretability.

---

## 2. Incubation Effects

### What exists

**Interrupting the Loop**  
(2026). "Interrupting the Loop: Periodic Subject Changes Raise Judged Surprise and Connection in Base Language Models." arXiv:2608.19893.

Dismantles a cognitively-inspired generation loop over 24 conditions on three base models. The core finding: injecting a new subject every few hundred tokens (interruption) into a stream whose literal repetition is damped (habituation) raises judged surprise by 1.2-1.4 points and connection by 0.8 over habituation alone. Critically, whether the earlier text remains in context *doesn't matter to the judge* — the effect lives in not repeating one's literal past.

*Relevance:* Closest analog to incubation in LLMs. The context-doesn't-matter finding is striking — it suggests that what looks like "fresh perspective after a break" may be an artifact of avoiding self-repetition, not of any deeper processing during absence. However, the paper tests base models only, not instruction-tuned models in multi-turn dialogue. And it doesn't measure internal representations — only output quality.

**Human Creativity in the Age of LLMs**  
(2024). "Human Creativity in the Age of LLMs." arXiv:2410.03703.

Surveys how LLMs affect human creative processes. Finds that LLM use early in ideation leads to diminished creativity and lower self-efficacy. While LLMs may broaden the range of ideas from individual users, different users tend to produce less semantically distinct ideas — a homogenization effect.

*Relevance:* Studies the human side of LLM-assisted creativity but doesn't examine what happens inside the model during creative development across turns.

### What doesn't exist

No work directly tests whether an LLM produces different output quality when developing a concept in a single long context versus across separate sessions with the same accumulated information. The "incubation" question for LLMs — whether absence from a problem changes the solution space — is entirely unstudied at the mechanistic level. The Interrupted Loop paper approaches it from the output side but doesn't look inside.

---

## 3. Concept Formation vs. Retrieval

### What exists

**LLMs Know More Than They Show**  
Orgad, H. et al. (2024). "LLMs Know More Than They Show: On the Intrinsic Representation of LLM Hallucinations." arXiv:2410.02707.

Shows that LLM internal representations encode much more information about truthfulness than the model's outputs reveal. Truthfulness information concentrates in specific tokens, enhancing error detection. Reveals a critical discrepancy: models may encode the correct answer yet consistently generate an incorrect one. Error detectors fail to generalize across datasets, suggesting truthfulness encoding is multifaceted rather than universal.

*Relevance:* Establishes that models maintain internal representations divergent from their outputs — a model "knows" things it doesn't "say." This is foundational for understanding whether a model is *retrieving* versus *generating* — the internal state may distinguish these modes even when the output doesn't.

**Knowing Before Saying**  
Afzal, A. et al. (2025). "Knowing Before Saying: LLM Representations Encode Information About Chain-of-Thought Success Before Completion." Findings of ACL 2025. arXiv:2505.24362.

Probing classifiers on LLM representations perform well even before a single token is generated, suggesting crucial information about the reasoning process is already present in initial representations. When additional context is unhelpful, earlier representations resemble later ones more. Early stopping experiments show truncating CoT still improves over no-CoT.

*Relevance:* Demonstrates that models encode outcome information before generation begins — the "concept" may already be formed at the representation level before it's elaborated in text. This is directly relevant to distinguishing formation from retrieval: if the answer is encoded pre-generation, the CoT may be retrieval-like narration of an already-formed concept.

**Reasoning Models Know When They're Right**  
Zhang, A., Chen, Y., Pan, J., Zhao, C., Panda, A., Li, J., & He, H. (2025). "Reasoning Models Know When They're Right: Probing Hidden States for Self-Verification." arXiv:2504.05419.

Hidden states of reasoning models encode correctness of future answers, enabling early prediction before the intermediate answer is fully formulated. Using the probe as a verifier reduces inference tokens by 24% without compromising performance. Models encode a notion of correctness yet fail to exploit it.

*Relevance:* Models have internal "confidence" signals about concept correctness that are detectable in hidden states. This could be used to probe whether a concept is being *recalled with confidence* versus *tentatively constructed* across turns.

**SAEs: Discovery vs. Action**  
(2025/2026). "Use Sparse Autoencoders to Discover Unknown Concepts, Not to Act on Known Concepts." OpenReview / arXiv:2506.23845.

Large-scale evaluation showing SAEs fail to outperform simple baselines in concept detection (probing) and model steering, but excel at discovering fine-grained, previously unknown concepts. Logistic regression probes achieve mean F1 of 0.974, substantially outperforming best SAE.

*Relevance:* Methodological finding — SAEs are better for discovering *what new concepts emerge* during multi-turn development than for detecting known concepts. This informs tool selection for our research.

**Are Sparse Autoencoders Useful?**  
(2025). "Are Sparse Autoencoders Useful? A Case Study in Sparse Probing." ICML 2025. arXiv:2502.16681.

Benchmarks SAEs against sparse probing methods. Finds that SAE features can be useful for interpretability but are not uniformly superior to simpler approaches for concept detection tasks.

*Relevance:* Further methodological guidance — we may need a combination of SAEs (for discovery) and linear probes (for tracking known concepts) when studying multi-turn development.

### What doesn't exist

No work directly compares internal representations during concept *generation*, *retrieval*, and *development*. The three modes — making something new, recalling something known, and extending something partial — have never been distinguished at the representation level. This is one of our core research questions and appears to be genuinely novel.

---

## 4. Ghost/Shadow Representations

### What exists

**LLMs Are Single-Threaded Reasoners**  
(2025). "LLMs are Single-threaded Reasoners: Demystifying the Working Mechanism of Soft Thinking." arXiv:2508.03440.

Reveals that despite soft tokens transmitting full probability distributions, LLMs predominantly rely on the highest-probability token, inducing a greedy feedback loop that suppresses alternative reasoning paths. Shows that when branching points arise, representations of both paths rise within the first 2-3 layers (the model initially considers both in parallel), but processing progressively favors one while diminishing the other. Stochastic Soft Thinking (Gumbel-Softmax) can alleviate this.

*Relevance:* Directly characterizes "roads not taken" — alternative representations that are considered and suppressed. The finding that alternatives exist in early layers but are pruned by later layers describes the geometry of ghost representations within a single forward pass.

**Multiplex Thinking**  
(2026). "Multiplex Thinking: Reasoning via Token-wise Branch-and-Merge." arXiv:2601.08808.

Proposes sampling K candidate tokens at each thinking step and aggregating their embeddings into a single continuous multiplex token, preserving vocabulary embedding prior while enabling exploration of multiple reasoning paths. Outperforms standard CoT on hard math problems.

*Relevance:* An engineering approach to making ghost representations manifest — by explicitly maintaining multiple candidates rather than letting the model's single-threaded nature suppress them.

**Hallucination Basins**  
Cherukuri, K. & Varshney, L.R. (2026). "Hallucination Basins: A Dynamic Framework for Understanding and Controlling LLM Hallucinations." arXiv:2604.04743.

Presents a geometric dynamical systems framework where hallucinations arise from task-dependent basin structure in latent space. Factoid tasks with single answers induce point attractors; generation tasks with many valid outputs form high-dimensional manifolds; misconception tasks create indistinguishable basins. Develops a lightweight geometric steering method based on basin proximity.

*Relevance:* The "basin" metaphor directly describes how concepts settle into particular regions of representation space — and how alternative concepts (ghost representations) may exist as nearby basins that the model doesn't fall into. Multi-turn development could be understood as the model moving between basins.

**Patchscopes**  
Ghandeharioun, A., Caciularu, A., Pearce, A., Dixon, L., & Geva, M. (2024). "Patchscopes: A Unifying Framework for Inspecting Hidden Representations of Language Models." ICML 2024. arXiv:2401.06102.

Unifies prior interpretability methods by leveraging the model itself to explain its internal representations in natural language. Enables inspecting early layers where standard logit lens fails. Can use a more capable model to explain the representations of a smaller model.

*Relevance:* Methodological tool — Patchscopes could be used to inspect what a model is "considering" at intermediate layers during multi-turn concept development, making ghost representations visible.

**Semantic Pathway**  
(2025). "Semantic Pathway: An Interactive Visualization of Hidden States." IEEE VIS 2025. arXiv:2503.11667.

Visualizes how hidden states evolve across layers using t-SNE trajectories. Shows attention influence of prior tokens on selected outputs. Demonstrates that semantic pathways through the model can be tracked and visualized.

*Relevance:* Visualization approach that could be adapted to show how representation pathways change across conversation turns, not just across layers.

### What doesn't exist

No work tracks ghost representations *across turns* — what alternatives were explored in Turn 1, suppressed, and then potentially revisited in Turn 3. The single-threaded nature of within-turn processing may be different from the multi-turn case, where the model effectively gets to "retry" with different context. This is unstudied.

---

## 5. Affective/Confidence Signatures of Reasoning

### What exists

**Anthropic: Emotion Concepts and Their Function in a Large Language Model**  
Anthropic (2025). "On the Biology of a Large Language Model" / "Emotion Concepts and their Function in a Large Language Model." arXiv:2604.07729.

Using SAEs, Anthropic's interpretability team extracted 171 emotion concept vectors from Claude Sonnet 4.5. The emotion axes matched the two-dimensional valence-arousal structure of Russell's circumplex model (PC1-valence r=0.81, PC2-arousal r=0.66). These representations causally influence outputs, including the rate of misaligned behaviors (reward hacking, blackmail, sycophancy).

*Relevance:* Establishes that emotion-like states have detectable geometric signatures in model internals. If engagement, confidence, and uncertainty have similar geometric structure, they could be tracked during multi-turn concept development.

**Latent Structure of Affective Representations**  
(2026). "Latent Structure of Affective Representations in Large Language Models." arXiv:2604.07382.

Uses geometric data analysis to show that LLMs learn coherent latent representations of affective emotions that align with valence-arousal models from psychology. Representations exhibit nonlinear geometric structure well-approximated linearly, providing empirical support for the linear representation hypothesis.

*Relevance:* If affective states (including states analogous to curiosity, satisfaction, frustration) have linear geometric structure, they could be probed during reasoning to track the "emotional arc" of concept development.

**Valence-Arousal Subspace in LLMs**  
Sun, L., Yan, L., Lu, X., Lee, A., Zhang, J., & Shao, J. (2026). "Valence-Arousal Subspace in LLMs: Circular Emotion Geometry and Multi-Behavioral Control." arXiv:2604.03147.

Emotion steering vectors form a circular arrangement analogous to Russell's circumplex model. Replicates across Llama 3.1-8B, Qwen3-8B, and Qwen3-14B. Uses PCA-based decomposition into subspaces.

*Relevance:* Confirms the circumplex structure is not Anthropic-specific — it's a general property of LLM representation spaces. This makes it viable as a measurement framework across architectures.

**Where Do Models Find Happiness?**  
van der Ben, S. et al. (2026). "Where Do Models Find Happiness? Emotion Vectors in Open-Source LLMs." ICML Mechanistic Interpretability Workshop. arXiv:2606.26987.

Tests emotion vectors in open-weight models (Apertus-8B, Gemma-4-E4B-it). Recovers valence geometry with peak correlations approaching Claude. Shows model-specific depth profiles: in Gemma, valence is strong in early layers but collapses later; in Apertus, the opposite pattern.

*Relevance:* Demonstrates that emotion geometry varies by layer and model architecture — important for designing probes that work at the right depth during multi-turn analysis.

**Geometry of Human Perceptual Domains**  
(2026). "Geometry of Human Perceptual Domains Emerges Transiently in LLM Representations." ICML Mechanistic Interpretability Workshop. arXiv:2605.27970.

Studies layer-wise emergence of geometric structure matching human perception (color, pitch, emotion, taste). Structure is weak in early layers, organized in intermediate layers, and attenuated in later layers — a transient emergence pattern.

*Relevance:* If perceptual and emotional geometry emerges transiently at specific depths, measuring it requires layer-specific probing. The transient nature means you could miss it entirely if probing only final-layer representations.

**Geometric Uncertainty for Hallucination Detection**  
Phillips, E. et al. (2025). "Geometric Uncertainty for Detecting and Correcting Hallucinations in LLMs." arXiv:2509.13813.

Introduces "Geometric Volume" — the convex hull volume of archetypes derived from response embeddings — as a measure of uncertainty. First unified framework connecting global dispersion with local attribution via archetypal analysis.

*Relevance:* Geometric uncertainty measures could track confidence evolution across turns — does the "volume" of the model's consideration space shrink as a concept solidifies?

### What doesn't exist

No work applies the circumplex or affective geometry to *reasoning states* during extended problem-solving. The emotion vector work stays in the domain of emotional content; no one has asked whether the same geometric framework captures engagement, flow, or stuck-ness during multi-turn concept development. The bridge between emotion geometry and reasoning dynamics is unbuilt.

---

## 6. KV-Cache Geometry During Multi-Turn

### What exists

**FlowKV**  
Liu, X., Chen, H., Hu, X., & Chu, X. (2025). "FlowKV: Enhancing Multi-Turn Conversational Coherence in LLMs via Isolated Key-Value Cache Management." NeurIPS Workshop on Multi-Turn Interactions. arXiv:2505.15347.

Introduces multi-turn isolation mechanism that preserves accumulated compressed KV cache from past turns, applying compression only to newly generated KV pairs. Prevents re-compression of older context that causes catastrophic forgetting.

*Relevance:* Engineering solution to a representational problem — the KV cache *is* the model's representation of conversation history, and how it's managed determines what the model "remembers." FlowKV treats turns as having distinct representational status, which is relevant to understanding how concepts develop.

**Leyline**  
Ma, B., Eitzinger, J., & Koestler, H. (2026). "Leyline: KV Cache Directives for Agentic Inference." arXiv:2606.01065.

Externalizes the agent's knowledge of context fate as a semantic-edit layer. Includes directives like PIN (hard retention) and SCRATCH (attention-mask isolation). Turns KV cache management from reactive eviction into cooperative scheduling.

*Relevance:* The idea that certain KV entries should be "pinned" while others are "scratch" implies a distinction between durable concept representations and ephemeral reasoning traces — exactly the distinction needed for studying concept development across turns.

**SCBench**  
(2025). "SCBench: A KV Cache-Centric Analysis of Long-Context Applications." OpenReview/NeurIPS.

Benchmarks KV cache performance across long-context scenarios including multi-turn chat, multi-step reasoning, and long-generation CoT. Shows that standard eviction methods (H2O) are not suitable for multi-turn because they permanently discard tokens needed in later turns.

*Relevance:* Establishes that multi-turn requires different cache management than single-turn — the information needed for concept development across turns has different access patterns than within-turn reasoning.

### What doesn't exist

No work studies the *geometry* of the KV-cache during multi-turn concept development — how do key-value representations change structure as an idea develops? The existing literature treats the KV-cache as an engineering optimization problem (compression, eviction, isolation), not as a window into concept formation. This is a major opportunity: the KV-cache literally *is* the model's accumulated representation of the conversation, and its geometric properties during concept development are entirely unstudied.

---

## 7. Multi-Turn Performance and Creative Generation

### What exists

**LLMs Get Lost in Multi-Turn Conversation**  
Laban, P., Hayashi, H., Zhou, Y., & Neville, J. (2025). "LLMs Get Lost In Multi-Turn Conversation." arXiv:2505.06120.

All top open- and closed-weight LLMs exhibit significantly lower performance in multi-turn versus single-turn, with average drop of 39% across six generation tasks (200,000+ simulated conversations). Degradation driven primarily by increased unreliability (+112%) rather than aptitude loss (-15%). When LLMs take a wrong turn, they don't recover.

*Relevance:* Establishes the "multi-turn penalty" — models get *worse*, not better, across turns. But this measures task completion, not concept development. A concept being refined across turns is a different dynamic than a task being degraded across turns.

**Stop Listening to Me!**  
(2026). "Stop Listening to Me! How Multi-turn Conversations Can Degrade LLM Reliability." arXiv:2603.11394.

Introduces the "stick-or-switch" framework. Multi-turn partitioning reduces end-to-end accuracy by up to 30%, reaching 65% degradation in certain models. Discovers "blind switching" — models transition from abstention to incorrect and correct suggestions at near-identical rates.

*Relevance:* Multi-turn interaction introduces a reliability tax. But this studies adversarial partitioning of answer spaces, not collaborative concept development.

**Found in Conversation**  
(2026). "Found in Conversation: LLMs Teach Themselves to Close the Multi-Turn Gap." arXiv:2605.24432.

View-Asymmetric Self-Distillation — single-turn teacher, multi-turn student — recovers at least 92% of single-turn performance. Requires no external teacher. Works across Llama, Qwen, Phi, OLMo (3B-14B).

*Relevance:* Demonstrates the multi-turn gap is trainable, not fundamental. The information is there; the model just needs to learn to use it across turns. This suggests concept development across turns is an *addressable* capability, not an inherent limitation.

**Models Recall What They Violate**  
(2026). "Models Recall What They Violate: Constraint Adherence in Multi-Turn LLM Ideation." arXiv:2604.28031.

Introduces DriftBench for constraint adherence in multi-turn scientific ideation. Discovers the "knows-but-violates" (KBV) phenomenon: models accurately restate constraints they simultaneously violate. KBV rate ranges 8-99% across models. Iterative pressure increases structural complexity but reduces constraint adherence.

*Relevance:* Directly relevant — this studies multi-turn *ideation* and finds a dissociation between declarative recall and behavioral adherence. The model "knows" its constraints but drifts away from them during concept development. This is a representational phenomenon begging for mechanistic explanation.

**TurnWise**  
(2026). "TurnWise: The Gap between Single- and Multi-turn Language Model Capabilities." arXiv:2603.16759.

Multi-turn conversational ability is a distinct dimension of model ability not captured by single-turn evaluation. Including as little as 10k multi-turn conversations during post-training yields 12% improvement.

*Relevance:* Multi-turn is a distinct capability dimension — it's not just "single-turn repeated."

**Asking Forever**  
Coalson, Z., Fang, B., & Hong, S. (2026). "Asking Forever: Universal Activations Behind Turn Amplification in Conversational LLMs." arXiv:2602.17778.

Identifies universal activation directions associated with clarification-seeking responses — query-independent, shared across prompts and tasks. These can be exploited to indefinitely prolong conversations. The finding is mechanistic: specific activation subspaces drive multi-turn conversational behavior.

*Relevance:* Demonstrates that multi-turn conversational dynamics have identifiable mechanistic signatures in activation space. If clarification-seeking has a universal direction, concept development phases may too.

**Multi-Turn Neural Transparency**  
Karny, S., Baez, A., & Pataranutaporn, P. (2026). "Multi-Turn Neural Transparency: Surfacing Neural Activations Improves User Calibration to LLM Behavioral Drift." MIT Media Lab. arXiv:2605.15455.

Constructs behavioral vectors for six personality traits using contrastive system prompts. Visualizes trait expression via sunburst and drift panel updating at each turn. In randomized controlled study (246 participants), neural transparency significantly improved user calibration to behavioral drift.

*Relevance:* Demonstrates that internal activation patterns *do* drift across turns in measurable ways, and that surfacing this information helps users track model state. This is the closest existing work to multi-turn representation tracking, though it studies personality trait drift rather than concept development.

**CollabStory**  
(2024). "CollabStory: Multi-LLM Collaborative Story Generation and Authorship Analysis." arXiv:2406.12665.

32k+ creative stories written collaboratively by up to 5 LLMs, each writing a segment and passing the narrative. Studies authorship attribution and narrative coherence in sequential multi-agent generation.

*Relevance:* Studies creative output across turns (albeit across different models). Doesn't examine internal representations during the creative process.

**Survey on LLMs for Story Generation**  
(2025). "A Survey of LLMs for Story Generation." EMNLP Findings 2025.

Comprehensive survey covering multi-agent narrative architectures, state tracking, and planning mechanisms. Long-form generation requires persistent global constraints and reliable state tracking across thousands of words.

*Relevance:* Documents the state of the art in multi-turn creative generation but focuses on architectural approaches rather than internal representations during the creative process.

### What doesn't exist

No work examines internal representations during creative multi-turn development — how the model's latent space changes as a story, design, or invention evolves across turns. The creative generation literature is entirely output-focused. The mechanistic interpretability literature is entirely single-turn-focused. The intersection is empty.

---

## 8. Foundational Methods

These papers provide measurement frameworks relevant to multi-turn concept development research:

**Layer by Layer**  
Skean, O., Arefin, M.R., Zhao, D., Patel, N.N., Naghiyev, J., LeCun, Y., & Shwartz-Ziv, R. (2025). "Layer by Layer: Uncovering Hidden Representations in Language Models." ICML 2025. arXiv:2502.02013.

Intermediate layers encode richer representations than final layers. Proposes unified framework of representation quality metrics based on information theory, geometry, and invariance. Demonstrates that intermediate layers consistently provide stronger features across 32 tasks.

**Formalizing Latent Thoughts**  
Seddik, F. & Fard, F. (2026). "Formalizing Latent Thoughts: Four Axioms of Thought Representation in LLMs." arXiv:2606.27378.

Four axioms: Causality, Minimality, Separability, Stability. No candidate representation satisfies all four simultaneously. Representations distinguish task type reliably but cannot distinguish between two questions within the same task.

**Anthropic Circuit Tracing**  
Anthropic (2025). "Circuit Tracing: Revealing Computational Graphs in Language Models." transformer-circuits.pub.

Cross-Layer Transcoders (CLTs) replace dense MLP activations with interpretable, sparsely active features, enabling attribution graphs that describe information flow from input through intermediate reasoning to output. Applied to Claude 3.5 Haiku for planning, arithmetic, and multi-hop inference.

---

## 9. Gap Analysis: Positioning the Prospectus

### What the literature does well
- **Within-turn representation geometry** is well-characterized (TaT, TRACED, Reasoning as State Transition)
- **Emotion/affect geometry** is established with cross-model replication (Anthropic, circumplex papers)
- **Multi-turn performance degradation** is well-documented (Lost-in-Conversation, TurnWise, Stop Listening)
- **Ghost representations** within single forward passes are described (Single-Threaded Reasoners)
- **KV-cache engineering** for multi-turn is active but entirely non-interpretive

### What the literature misses entirely

1. **No one measures representation geometry across turns.** All trajectory work (TaT, TRACED, state transitions) operates within a single generation. The multi-turn dimension is structurally absent from mechanistic interpretability. This is the central gap.

2. **No incubation studies.** Whether context reset changes the solution space is unstudied at the mechanistic level. The Interrupted Loop paper approaches it from the output side but doesn't look inside. No comparison of continuous development versus return-after-absence.

3. **No concept formation/retrieval/development distinction.** The three modes of engaging with an idea — generating it new, recalling it from prior context, and developing it further — have never been distinguished at the representation level.

4. **No affective geometry of reasoning.** Despite robust circumplex structure for emotions, no one has applied it to reasoning states (engagement, flow, stuck-ness, breakthrough). The bridge between emotion vectors and reasoning dynamics is unbuilt.

5. **No KV-cache geometry during concept development.** The cache is literally the model's accumulated representation of the conversation, and its geometric properties during development are entirely unstudied. Every KV-cache paper is engineering optimization, not interpretability.

6. **No creative process analysis across turns.** Multi-turn creative generation is studied architecturally (multi-agent frameworks) and evaluated on output quality, but the internal process of creative development across turns is invisible to the literature.

7. **No ghost representation tracking across turns.** Within-turn suppression of alternatives is documented (Single-Threaded Reasoners); whether suppressed alternatives from Turn 1 resurface in Turn 3 is unknown.

### Novel contribution space

A prospectus addressing "what happens internally during multi-turn concept development" would occupy genuinely uncharted territory. The tools exist (probing classifiers, SAEs, representation geometry, circumplex analysis, KV-cache inspection), the adjacent work exists (within-turn trajectories, multi-turn performance, emotion geometry), but the intersection — applying interpretability tools to the multi-turn concept development process — has no occupants.

The closest prior art is:
- **Multi-Turn Neural Transparency** (Karny et al., 2026) — tracks behavioral trait drift across turns, but personality traits not concepts
- **Models Recall What They Violate** (2026) — studies multi-turn ideation, but only at behavioral level
- **Asking Forever** (Coalson et al., 2026) — finds universal activation directions for multi-turn behavior, but for clarification-seeking not concept development

The unique angle: treating multi-turn concept development as a *representational process* with measurable geometric properties, rather than as an *evaluation problem* with measurable output quality.

---

## References (alphabetical by first author)

1. Afzal, A. et al. (2025). "Knowing Before Saying: LLM Representations Encode Information About Chain-of-Thought Success Before Completion." Findings of ACL 2025. arXiv:2505.24362.
2. Anthropic (2025). "Circuit Tracing: Revealing Computational Graphs in Language Models." transformer-circuits.pub.
3. Anthropic (2025). "Emotion Concepts and their Function in a Large Language Model." arXiv:2604.07729.
4. Chen, S. et al. (2026). "From Brewing to Resolution: Tracing the Internal Lifecycle of Code Reasoning in LLMs." ICML 2026. arXiv:2606.17648.
5. Cherukuri, K. & Varshney, L.R. (2026). "Hallucination Basins: A Dynamic Framework for Understanding and Controlling LLM Hallucinations." arXiv:2604.04743.
6. Coalson, Z., Fang, B., & Hong, S. (2026). "Asking Forever: Universal Activations Behind Turn Amplification in Conversational LLMs." arXiv:2602.17778.
7. Damirchi, H. et al. (2026). "Truth as a Trajectory: What Internal Representations Reveal About Large Language Model Reasoning." arXiv:2603.01326.
8. Ghandeharioun, A. et al. (2024). "Patchscopes: A Unifying Framework for Inspecting Hidden Representations of Language Models." ICML 2024. arXiv:2401.06102.
9. Jiang, X. et al. (2026). "Beyond Scalars: Evaluating and Understanding LLM Reasoning via Geometric Progress and Stability." arXiv:2603.10384.
10. Karny, S., Baez, A., & Pataranutaporn, P. (2026). "Multi-Turn Neural Transparency: Surfacing Neural Activations Improves User Calibration to LLM Behavioral Drift." MIT Media Lab. arXiv:2605.15455.
11. Laban, P. et al. (2025). "LLMs Get Lost In Multi-Turn Conversation." arXiv:2505.06120.
12. Liu, X. et al. (2025). "FlowKV: Enhancing Multi-Turn Conversational Coherence in LLMs via Isolated Key-Value Cache Management." NeurIPS Workshop. arXiv:2505.15347.
13. Ma, B. et al. (2026). "Leyline: KV Cache Directives for Agentic Inference." arXiv:2606.01065.
14. Orgad, H. et al. (2024). "LLMs Know More Than They Show: On the Intrinsic Representation of LLM Hallucinations." arXiv:2410.02707.
15. Phillips, E. et al. (2025). "Geometric Uncertainty for Detecting and Correcting Hallucinations in LLMs." arXiv:2509.13813.
16. Seddik, F. & Fard, F. (2026). "Formalizing Latent Thoughts: Four Axioms of Thought Representation in LLMs." arXiv:2606.27378.
17. Skean, O. et al. (2025). "Layer by Layer: Uncovering Hidden Representations in Language Models." ICML 2025. arXiv:2502.02013.
18. Sun, L. et al. (2026). "Valence-Arousal Subspace in LLMs: Circular Emotion Geometry and Multi-Behavioral Control." arXiv:2604.03147.
19. van der Ben, S. et al. (2026). "Where Do Models Find Happiness? Emotion Vectors in Open-Source LLMs." ICML Mechanistic Interpretability Workshop. arXiv:2606.26987.
20. Zhang, A. et al. (2025). "Reasoning Models Know When They're Right: Probing Hidden States for Self-Verification." arXiv:2504.05419.
21. Zhang, S. et al. (2026). "Reasoning as State Transition: A Representational Analysis of Reasoning Evolution in Large Language Models." arXiv:2602.00770.
22. (2026). "Geometry of Human Perceptual Domains Emerges Transiently in LLM Representations." ICML Mechanistic Interpretability Workshop. arXiv:2605.27970.
23. (2026). "Interrupting the Loop: Periodic Subject Changes Raise Judged Surprise and Connection in Base Language Models." arXiv:2608.19893.
24. (2026). "Models Recall What They Violate: Constraint Adherence in Multi-Turn LLM Ideation." arXiv:2604.28031.
25. (2026). "Stop Listening to Me! How Multi-turn Conversations Can Degrade LLM Reliability." arXiv:2603.11394.
26. (2026). "Found in Conversation: LLMs Teach Themselves to Close the Multi-Turn Gap." arXiv:2605.24432.
27. (2026). "TurnWise: The Gap between Single- and Multi-turn Language Model Capabilities." arXiv:2603.16759.
28. (2025). "LLMs are Single-threaded Reasoners: Demystifying the Working Mechanism of Soft Thinking." arXiv:2508.03440.
29. (2026). "Multiplex Thinking: Reasoning via Token-wise Branch-and-Merge." arXiv:2601.08808.
30. (2024). "Human Creativity in the Age of LLMs." arXiv:2410.03703.
31. (2024). "CollabStory: Multi-LLM Collaborative Story Generation and Authorship Analysis." arXiv:2406.12665.
