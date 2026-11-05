# Two Worlds, One Challenge: How AI Solves Unfamiliar Problems — Evidence from ARC-AGI-2 and ARC-AGI-3

*Separating adaptation policy, execution, behavior, and outcome*

Tsitsino Dzotsenidze · ORCID 0000-0002-9432-1803

## Abstract

What evidence connects an adaptation procedure to better problem solving? We examine two bounded studies of existing ARC solvers rather than equate intervention execution with success. In a Qwen3-based port of NVARC for static ARC-AGI-2, an updating configuration exceeded a zero-learning-rate configuration by 21.39 and 20.98 rounded public-score points in initial and repeated evaluations. In interactive ARC-AGI-3, recovery telemetry establishes that context-rebuilding interventions executed and were followed by environment actions, but not that they improved outcomes. A retained LP85 trace reveals 962 actions with no visible board change, compared with 18 state-changing actions to completion in a different configuration. We propose reporting configuration, intervention execution, subsequent behavior, and task outcome separately. These studies do not identify a universal causal mechanism for adaptation.

## Problem and prior work

ARC-AGI-2 tests inference of transformations from example grids; ARC-AGI-3 requires acting in unfamiliar interactive environments [1]. The tasks, outcome metrics, and adaptation mechanisms differ. We therefore do not pool benchmark scores or treat the two studies as matched experimental arms. Instead, we ask what links must be documented before an adaptation procedure can be called useful.

NVARC, by Ivan Sorokin and Jean-Francois Puget, already incorporates per-puzzle LoRA test-time fine-tuning and builds on ARChitects [2]. We examine an attributed Qwen3-based port, not a newly invented solver or adaptation method. The ARC3 agent derives from the Qwen3.8 Flash Next NVFP4 and Tufa Labs/Duck, TAAF, and Keith Tyser serving lineage [4]. We report a repeated update-policy comparison in ARC2 and use recovery logs and a selected trajectory to distinguish action production from progress in ARC3.

## ARC-AGI-2: repeated configuration contrast

The port retains upstream adaptation, candidate generation, and ranking. B1 uses learning rate 5e-5; Z1 sets the learning rate to zero while preserving trainer and optimizer computations. Both configure `PYTHONHASHSEED=42` before driver launch. For each condition, the repeat submission used a local notebook source byte-identical to its initial configuration [3].

| Configuration | Initial public score | Repeat public score |
| --- | ---: | ---: |
| B1 (updates enabled) | 30.97 | 30.56 |
| Z1 (zero learning rate) | 9.58 | 9.58 |
| B1 minus Z1 | 21.39 | 20.98 |

The gap recurred, supporting a substantial descriptive association between update policy and public performance **in this implementation**. Both repeat submissions were marked “Succeeded (after deadline)”: they are research observations, not timely competition entries. The unpinned-seed historical B0 score (31.81) remains outside this comparison; private scores are unknown [3].

This comparison is not proof that gradient updates alone caused each solved task. Two observations per condition do not establish statistical significance or population repeatability; identical notebook sources and seed settings do not guarantee identical resource use or execution. Updates may also change candidate generation, ranking, and compute allocation. A separate 128-step synthetic test found 506 inspected adapter tensors finite and unchanged under zero learning rate, but stopped before decoding; it checks parameter invariance, not equivalent benchmark predictions [3].

## ARC-AGI-3: recovery execution versus progress

The interactive agent alternates analysis, tool calls, and environment actions. K0 disables the specified response-recovery path. K1 makes recovery eligible after a completed model response yields neither a semantic tool call nor an executed environment action. Visible-immobility recovery (VIR) requires at least 20 consecutive unchanged full-grid transitions spanning at least five minutes without level completion. A visible change, completion, RESET, or terminal state invalidates eligibility. Before recovery at a model-control boundary, the system revalidates the recorded state; VIR also rechecks unchanged-transition count, elapsed span, and frame identity. Each mechanism allows at most one attempt per game; at most one attempt occurs per analysis entry, with VIR priority when both are eligible [4].

**What recovery changes.** In the retained K1+VIR implementation, the recovery rebuilds the next model request from the system instructions and current observation. It discards the accumulated conversation, captured reasoning, and current plan while retaining structured notes (world, goal, and action models, recent findings, open questions, and cross-level notes). It reintroduces current frame, runtime history, available actions, and the latest result, and asks for a fresh plan; VIR explicitly warns that retained notes may be stale. If construction fails, the original conversational history and structured notes are retained [4]. This is selective context reconstruction, not erasure of all task knowledge or a direct change to the environment.

The October public scores were K0 3.32 and K1+VIR 2.61; historical K2 was 3.10 and baseline V11 3.07 [4]. K2 denotes a separate earlier recovery-enabled carrier, not another label for the later K1+VIR policy. The earlier K0/K2 diagnostic and later K1+VIR development run must not be merged. These submissions differ in provenance and are not replicated matched arms; their ranking does not estimate recovery's causal effect.

In the retained K1+VIR PUBLIC25 development run, 40 of 183 levels were completed across 25 games, with no full-game win, 7,687 actions, and mean score 8.3771. Logs record eight K1 and one VIR attempt, each associated with applied context and a subsequent environment action [4]. The recorded chain demonstrates context application followed by an environment action; it does **not** establish that the intervention produced useful progress. Development scores are not official submission scores.

**The LP85 diagnostic.** In an earlier K0/K2 comparison, level 4 started from equal visible boards. K0 completed the level in 18 actions, each changing the full board. K2 executed 962 mouse actions without a full-grid change or level completion. None fell within 16 bounding boxes around candidate arrow-like glyphs, four of which corresponded to K0's effective clicks [5]. The trace demonstrates sustained visible immobility and clicks that produced no visible board change or level completion; it does not prove the glyphs were universally valid controls or that clicking them would have rescued K2. Equal visible frames do not guarantee equal hidden state or context, and no counterfactual click was replayed.

A prefix-only full-grid detector retrospectively became eligible at cumulative action 70 and remained eligible at the next analyzer boundary after action 77; it produced no alert in five other selected game-arm traces. A broader time/action-without-completion rule also flagged trajectories that subsequently completed levels [5]. The rules were fixed after observing LP85; selected-case discrimination is not population detection accuracy. Detecting a stalled visible state does not show that context recovery repairs the underlying action-selection failure.

## Synthesis, limitations, and reproducibility

The studies illuminate different links in an evidence chain: **(1)** specify the intervention or configuration; **(2)** verify that it actually executes; **(3)** inspect the resulting actions and state changes; and **(4)** measure task outcomes under a defensible comparison. ARC2 supplies a repeated configuration-to-outcome contrast but does not isolate the internal pathway. ARC3 supplies execution telemetry and a concrete behavior-level diagnostic without a matched demonstration of better outcomes. This is a proposed reporting discipline, not a general theory of intelligence.

A testable design hypothesis follows: recovery triggers should correspond to the failure being treated. A response without a tool call and repeated clicks that leave a board unchanged are observably different failure modes; restarting action production alone may not improve control selection. Prospective, better-matched trajectory comparisons would need intervention timing, task-level outcomes, and realized resource use.

Separate Zenodo archives document ARC2 and ARC3 sources and evidence [3,4]. They exclude model weights and hidden evaluation data. The ARC3 release additionally excludes private SQLite material and two full upstream-derived overlays pending license clarification, so it is not wholly self-contained. The LP85 diagnostic material is available in the separately published supplement [5]. Broader claims about education, economic outcomes, or human–AI coexistence need independent evidence.

## Conclusion

The ARC2 update-policy advantage recurred, whereas ARC3 telemetry confirms recovery execution without establishing recovery benefit. LP85 shows how abundant actions can coexist with no visible progress. Together these results specify the evidence still needed to connect adaptation interventions to successful problem solving.

## References

[1] ARC Prize (2026). [ARC-AGI-2 competition](https://www.kaggle.com/competitions/arc-prize-2026-arc-agi-2); [ARC-AGI-3 benchmark](https://arcprize.org/arc-agi/3).

[2] Sorokin, I., and Puget, J.-F. (2025). [NVARC solution to ARC-AGI-2 2025](https://github.com/1ytic/NVARC).

[3] Dzotsenidze, T. (2026a). ARC-AGI-2 source and evidence archive, version 2026-final-1. Zenodo. [doi:10.5281/zenodo.23895859](https://doi.org/10.5281/zenodo.23895859).

[4] Dzotsenidze, T. (2026b). ARC-AGI-3 source and evidence archive, corrected release. Zenodo. [doi:10.5281/zenodo.23869166](https://doi.org/10.5281/zenodo.23869166).

[5] Dzotsenidze, T. (2026c). LP85 Control and Progress Diagnostic (21 October). Public selected-case evidence supplement. [GitHub, immutable commit aab3894](https://github.com/tsitsinodzotsenidze-eng/arc-agi3-agent-strategy/tree/aab389457620273417a8345b161f1c9028ac55d1/publication/lp85-supplement-2026-11-05).
