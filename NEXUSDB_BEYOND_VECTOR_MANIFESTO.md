# NexusDB — An Accessory Brain for Any AI Model

*X Quantum Tech FZE LLC · November 2026 · Italian version: [MANIFESTO_COGNITIVO_NEXUSDB.md](MANIFESTO_COGNITIVO_NEXUSDB.md)*

> “NexusDB” is a working name. It is not the NexusDB database for Delphi (nexusdb.com); the final product name will be chosen before launch.

## 1. The problem

Every organization wants its AI to know its people, prices, rules and procedures — and to stay current. The three tools available today each break somewhere:

- **Instruction files** (`.md`, prompts) are static text pasted into every request. Cost grows with every page, past a few dozen pages they no longer fit, and they never learn which answer was right.
- **Fine-tuning** freezes knowledge into weights. It is costly, redone at every change, unavailable on closed API models — and in our tests it made a model stop citing its sources.
- **Vendor memory** lives inside one assistant: opaque, tied to that vendor, focused on facts and preferences rather than procedures, and lost when you change model.

What is missing is a memory that belongs to the customer, works with any model and learns how the work is done.

## 2. The idea

The model is the cortex: general language and reasoning, identical for every customer. What makes a professional valuable is different — remembering what happened, knowing which step comes next, turning repeated work into habit. In the brain those are separate systems. In AI they should be separate, portable and owned.

A NexusDB **cartridge** is one portable file (`.nxm`) that sits beside any model, open or closed, small or large. It holds:

1. **Knowledge** — documents and work history with hybrid search (semantic + keyword) and exact source citations. *In the Rust engine.*
2. **Experience** — every verified answer becomes a new way in to its source, used only when a new question is similar enough (a similarity gate), reversible, per customer. *In the Rust engine.*
3. **Learned mini-brains** — small networks (1–3M parameters, CPU, trained from use in seconds) that decide where to look and what comes next. *Measured in the lab; next engine integration.*
4. **Reranking** — a second pass over the first 50 candidates. *Measured in the lab; next engine integration.*

The cartridge learns the roads between the facts, not new facts. The model stays unchanged, so the experience survives a change of model or vendor.

## 3. What we measured (November 2026)

Demo-scale evidence: single rented GPUs, open models of 1–4 billion parameters, a two-person core team.

| ID | Claim | Result | Test |
|---|---|---|---|
| E1 | A small model with our search matches frontier models | 69.6% vs 68.6–71.0% | 400 questions, blind judge; Gemma 3 4B vs four frontier models given the same cartridge with standard search |
| E2 | No training beats fine-tuning, same model | +32.6 vs +8.9 points | Gemma 3 1B: cartridge with no training vs fine-tuning on all passages |
| E3 | Learns from verified use, no retraining | +11.2 points (48.1 → 59.4%) | 26,682 passages, unseen wordings, 3 seeds; damage on untouched topics −0.4 |
| E4 | The gain is the system, not the vectors | −29.0 vs −0.4 damage | Ablation: copying past questions as vectors breaks other searches; the gated system does not |
| E5 | Knows the next step of a procedure | 2.6% → 50.5% | 400 public procedures hidden among 14,627 passages, 3 seeds |
| E6 | Learned networks beat hand-written rules on real work | +5.2 / +7.1 points | 849 real notes + 823 sessions; 2 splits; thresholds fixed in advance |
| E7 | Learns by being talked to, with fewer tokens | 71% vs 5% · 4.6× fewer input tokens | Live chat with a 4B model; 8 dialogues vs the whole memory pasted as a file |
| E9 | Public benchmark LongMemEval | **85.6%** with GPT-4o · 79.6% vs Zep 63.8% with GPT-4o-mini | 500 questions, official judge — every answer public |

Full LongMemEval record: **[xquantumtech.com/benchmark/longmemeval](https://xquantumtech.com/benchmark/longmemeval/)** · files: **[nexusdb-longmemeval](https://github.com/xQUANTUMTECH/nexusdb-longmemeval)**.

## 4. How we test

- **Thresholds before results.** Pass criteria (minimum gain, maximum damage) are written before each run; failed configurations are reported as failures, not re-tuned on the test.
- **Never-seen material.** New wordings, questions written by other model families, notes and sessions held out from training; damage always measured on untouched topics.
- **Repeats and intervals.** Three seeds or two independent splits, 95% confidence intervals, counts of improved vs worsened questions.
- **Blind, independent judge.** A model that is not a competitor and does not know which system answered.
- **Negative results kept.** Failed ideas stay in the register.
- **Independent review.** A separate AI system acted as devil's advocate on the learning code; the defects it found were fixed and the affected numbers re-measured and lowered.

## 5. Limits we state

- Results are demo-scale; the legal, science and agent suites (E8) are small and early.
- In E1 the frontier models used standard search; the precise claim is “a 4B model with our search equals frontier models with a standard search”.
- In E3 the teacher is the verified reference passage; with a fully automatic teacher the gain so far is +5.6 points (1 seed).
- LongMemEval tests recall from short synthetic chats; systems using newer answer models (e.g. gpt-5-mini) are not comparable with our GPT-4o runs.
- Our own embedder (67% of bge-m3) and reader are research, not product.

## 6. What this manifesto replaces

The April 2026 version of this document described a “DNA-Φ quantum-inspired manifold”, phase-based scoring and other mechanisms that were never shown to beat a plain hybrid baseline in an ablation. Those claims are withdrawn. Everything above is measured, with its method and limits.

## Contact

Maurizio Tarricone — Founder & CEO, X Quantum Tech FZE LLC (UAE)
ceo@xquantumtech.com · [xquantumtech.com/it/nexusdb](https://xquantumtech.com/it/nexusdb)

The engine is proprietary. Scripts, logs and the full evidence register are available to investors and design partners on request, under NDA.

© 2026 X Quantum Tech FZE LLC.
