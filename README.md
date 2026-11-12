# VeriMotion: Layer-Wise Language-to-Motion Planning with Shared STL Robustness Feedback

This repository contains the anonymous code release of **VeriMotion**, a closed-loop
architecture for translating natural-language instructions into executable robot
motion plans under spatial, temporal, and logical constraints.

![Framework Overview](T3%20Planner%20Framework.png)

## Overview

Natural-language robot instructions may couple *what* must be achieved, *in which
order*, *within what time intervals*, and *under which motion constraints*.
VeriMotion resolves such instructions across three representations and
granularities:

1. **Task Planner** — resolves spatial predicates, implicit prerequisites,
   conditional branches, and ordering relations into an untimed subgoal sequence.
2. **Time Planner** — assigns timestamps to the subgoal sequence, instantiating
   deadlines, durations, and operating windows.
3. **Trajectory Planner** — generates an executable motion controller (Python
   code) that realizes the timed waypoints under kinematic and velocity limits.

Each layer is verified against a **shared STL specification**. Negative STL
robustness identifies the layer at which a violation appears and triggers
regeneration at that layer, which limits error propagation while retaining a
route for cross-layer repair. The trajectory layer additionally uses a dual-layer
motion verification loop (Python syntax pre-compilation + STL checking of
simulated trajectories against a case library).

The framework supports multiple LLM backbones, including frontier API models and
a distilled **Qwen3-4B** variant for edge deployment.

## Repository Scope

This release supports the following workflows.

1. Run VeriMotion end-to-end on the released test scenarios.
2. Evaluate success rate (SR), average LLM calls (AC), and average call time (AT)
   against the reported baselines.
3. Inspect the prompting scheme, verification code, and the case library.
4. Reproduce the ablation variants (w/o self-correction, w/o decomposition).



## Scenarios and Datasets

Three simulated 2D scenarios are supported. Test sets pair each natural-language
instruction with a manually verified ground-truth STL specification, isolating
translation errors from planning evaluation.

| Scenario   | Description                                                        | Test samples |
|------------|--------------------------------------------------------------------|--------------|
| Household  | 8-room household environment with labeled spatial regions          | 400          |
| Chip       | Sequential key–door task scenario adapted from Chip's Challenge    | 300          |
| Navigation | Battery-constrained navigation with dynamic charging-station visits| 300          |

Instructions are additionally categorized into four complexity levels —
**Atomic, Basic, Composite, Complex** — by the number of temporal and spatial
operators in the STL formalization (see `docs/task_complexity.md`).

## Installation

```bash
git clone &lt;anonymous-repo-url&gt;
cd VeriMotion

python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
