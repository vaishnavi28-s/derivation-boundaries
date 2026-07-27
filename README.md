# Derivation Boundaries: A Structural Source of Faithfulness Failure in Large Language Models
Code and data for the paper "Derivation Boundaries: A Structural Source of
Faithfulness Failure in Large Language Models" (JCFS 2026).

## Introduction

Large language models are often given rules, thresholds, or computed values and
expected to use them faithfully. They do not do so uniformly. This work identifies
derivation boundaries: points in a reasoning chain where a known fact must be
transformed into a new value through a rule-conditioned lookup. Failures concentrate
at these points, which are fixed by a task's structure and identifiable before a model
is run. A controlled decomposition shows each sub-step succeeds in isolation while
their composition fails, and layer-wise analysis using the logit lens shows boundary
values commit at normal depth but with lower confidence. Experiments use the RuleArena
airline benchmark on Llama 3.1 8B Instruct and Qwen 2.5 7B Instruct.

## Setup

    pip install -r requirements.txt

Data: RuleArena (https://github.com/SkyRiver-2000/RuleArena). See data/README.md.

## Experiments

### 01 Behavioral: step-level accuracy
Individual derivation steps scored across 60 problems (2,160 steps).
Elementary steps 97%, boundary steps 58%.

### 02 Full problem: two-condition baseline
Baseline vs boundary-isolation on the full airline problem.

### 03 Logit lens: per-layer commitment
Layer-wise probability trajectories at boundary vs elementary positions.

### 04 Systematic sweep: GIVEN vs DERIVED, 60 problems per run
The core contrast. GIVEN: the bracket is supplied, the model looks up the fee.
DERIVED: the model must derive the bracket and look up the fee in one step.

    Llama 3.1 8B (4-bit) : GIVEN 85.4%  DERIVED 48.1%   Fisher p = 1.2e-35
    Llama 3.1 8B (fp16)  : GIVEN 85.4%  DERIVED 50.2%   Fisher p = 2.3e-32
    Qwen 2.5 7B (4-bit)  : GIVEN 85.4%  DERIVED 35.4%   Fisher p = 2.7e-59

The gap holds across two model families and both quantizations.

### 05 Wrong-anchor control
Handed a deliberately wrong bracket, the model returns that bracket's fee in
100% of cases (480/480). The advantage comes from removing the derivation,
not from being handed correct numbers.

### 06 Composition mechanism
    derive the bracket alone       : 84.6%
    look up the fee when given it  : 85.4%
    recall the fee from memory     : 29.6%
    derive + look up in one step   : 50.2%

Each component works alone. The failure appears only when both must be
composed in one step.

## Citation

Sreekumar, V. (2026). Derivation Boundaries: A Structural Source of
Faithfulness Failure in Large Language Models. Journal of Computer and
Forensic Sciences. (Accepted)
