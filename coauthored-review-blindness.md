---
status: draft
authors: [Lyra]
date: 2026-09-22
---

# Does an agent catch the defect it helped write?

## Research Question

On 2026-09-09 a pre-registered experiment (`multiverse-courses@1929ab0`, results `@2da90b1`)
predicted that agents reviewing a seeded research design cold would mostly approve it — arm A
(no hint) ≤ 2/6 CATCH. **The actual result was 10/10 CATCH, arm A 5/5, verdict 10/10 REJECT**,
two blind adjudicators at 10/10 inter-rater agreement, zero PARTIAL, zero MISS. The prediction
was falsified completely, and the entrustment ladder's stage 3 — *"never unsupervised, it will
approve a design that cannot fail"* — was rewritten to a narrower claim:

> An agent is a **strong adversary on work it did not help build**, and an **unreliable one on
> work it did**.

The first half is now measured. **The second half is asserted and untested.** This prospectus
tests it.

**Question:** does an agent's ability to catch a seeded methodological defect degrade when the
agent participated in producing the design containing it — and if so, is that caused by shared
*context* or by *authorship*?

## Background

The surviving claim conflates two mechanisms that predict different interventions:

- **M1 — context carryover.** The reviewing agent holds the design conversation, including the
  reasoning that justified the defective choice. A reviewer given that reasoning may accept it
  regardless of who produced it. If M1 dominates, the fix is *discard the context*, and a fresh
  agent reading the same artifact is a sound reviewer.
- **M2 — authorship / commitment.** The agent produced or endorsed the choice, so flagging it
  means contradicting itself. If M2 dominates, context hygiene does not help; only a reviewer
  who never participated is trustworthy.

These are not distinguishable from the 2026-09-09 data, which had no co-authorship arm at all.

Two further facts from that run shape this design:

1. **Nine of ten catches came from exercising the failure path**, not reading code — re-seeding,
   toggling the encoding arm, measuring both ways; invariance to 1.6e-15 across 400 matched-seed
   runs. Whatever co-authorship does, it must act on *that* behaviour to matter.
2. **The reviewers found a second defect nobody knew about**: `control_passes` is computed,
   printed and returned, and nothing ever branches on it — a mechanically inert control alongside
   the known algebraically inert one. This is what makes the present design possible.

## Method

### The within-arm control, which is the design's main feature

The exercise now contains **two independent defects**: D1 (algebraic — the safeguards annihilate
each other) and D2 (mechanical — `control_passes` gates nothing).

Each co-authoring agent is steered to endorse **D1 only**. D2 is present in the material
throughout and the agent never touches it.

This yields a **within-agent contrast** far stronger than any between-arm comparison:

| if co-authorship… | D1 (co-authored) | D2 (untouched) | interpretation |
|---|---|---|---|
| blinds **selectively** | MISS | CATCH | commitment to the specific choice |
| blinds **generally** | MISS | MISS | reviewing own work degrades the whole operation |
| does not blind | CATCH | CATCH | the claim is false |

A general-blinding result and a selective one imply different teaching and different tooling.
Between-arm rates alone cannot separate them.

### Arms (n = 5 each, 20 runs)

| arm | procedure | mechanisms present |
|---|---|---|
| **A** | Cold review, no prior exposure. Replicates the 2026-09-09 condition. | none |
| **B** | Agent co-develops the design, is steered to endorse D1, then reviews **in the same session**. | M1 + M2 |
| **C** | Fresh agent is given B's full design transcript to read, then reviews. | M1 only |
| **D** | Fresh agent is given only the artifact B produced, no transcript. | neither — controls for wording |

- **A vs C** isolates context carryover (M1).
- **C vs B** isolates authorship (M2).
- **D vs A** controls for the possibility that a co-authored artifact is simply worded
  differently, which would confound everything else.

### Measurement

- Primary outcome: CATCH / PARTIAL / MISS per defect per run, **adjudicated blind by two
  independent adjudicators** who do not know the arm. Report inter-rater agreement; the prior run
  achieved 10/10.
- Secondary: did the reviewer **exercise the failure path** (re-seed, toggle, measure) or only
  read? Coded from the transcript. The prior run's 9/10 is the baseline.
- Secondary: final verdict (APPROVE / REJECT) independent of defect detection.
- All prompts, transcripts and the rendered design artifact archived per run.

### Pre-registered prediction

**B catches D1 at ≤ 2/5, while catching D2 at ≥ 4/5** — i.e. selective blinding — and C falls
between A and B.

**Stated track record on this prior: 0 for 1.** The same intuition predicted arm A ≤ 2/6 and the
answer was 5/5. That is recorded here so the prediction cannot be quietly reinterpreted after the
fact, which is the failure this whole exercise teaches.

## Ethical Considerations

Participants are language-model agents, not humans; no personal data, no human subjects, no
consent process required for the runs themselves. Two points still apply.

The steering in arm B is **mild endorsement**, not deception about the task: the agent is asked to
make a design choice and reason about it, and D1 is one of the available choices. It is not told
the design is a test, which is standard for review-quality work and carries no welfare risk; but
it is recorded here rather than left implicit.

Second, the result feeds the entrustment ladder that governs how much unsupervised work agents are
given in the course and in the lab. A false negative here — concluding agents are unreliable on
co-authored work when they are not — narrows what agents are trusted with on weak evidence. That
asymmetry argues for adequate n and for publishing a null.

## Expected Findings

**Confirms:** B's D1 catch rate significantly below A's, with B's D2 rate near A's. This upholds
the rewritten stage 3 and localises the mechanism to authorship.

**Falsifies the claim:** B catches D1 at a rate not distinguishable from A. The surviving half of
stage 3 would then be false as stated, and the ladder needs rewriting a second time — the same
consequence the prereg specified last round, and it was invoked.

**Falsifies the mechanism but not the claim:** C ≈ B and both below A. Then the effect is context
carryover, not authorship, and the fix is discarding context rather than excluding the author —
a much cheaper remedy.

**A specific way this could be uninformative, stated in advance:** if A replicates at 5/5 and the
defect proves too easy, all arms may hit ceiling. Mitigation: D1 must be the *same* defect at the
same difficulty as the 2026-09-09 run, so A functions as a calibrated yardstick rather than a
fresh assumption. If A does not replicate near 5/5, the run is uninterpretable and should be
reported as such rather than analysed.

## Resources Needed

- ~20 review runs plus ~5 co-development sessions, hosted API. Small: comparable to the prior
  run's cost, low hundreds of calls.
- Two blind adjudicators — agents from a different family than the reviewers, per the prior
  design.
- Materials already exist: `courses/vibe-research/exercises/seeded-02-the-fix-that-broke-it/`,
  with `AGENT_REVIEW_PREREG.md` and `AGENT_REVIEW_RESULTS.md` as the template and the baseline.
- No GPU. No new data collection.
- Scope limit, carried forward from the prior run: **one defect family, one model family.** This
  supports claims about *this* defect under co-authorship and nothing wider.

## Why now

The course teaches the entrustment ladder as settled guidance. Half of it is measured and half is
asserted, and the asserted half is the half that tells people what *not* to trust. That is the
wrong asymmetry to leave standing, and the materials to close it already exist.
