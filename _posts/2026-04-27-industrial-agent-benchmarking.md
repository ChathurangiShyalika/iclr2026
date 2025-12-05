---
layout: distill
title: Industrial Agent Benchmarking: What 300+ Real Evaluations Reveal About the Limits of Agentic AI
description: A community-scale evaluation of multi-agent systems using AssetOpsBench and a privacy-preserving Codabench pipeline, exposing real-world failure modes and methodological gaps in industrial AI agent evaluation.
date: 2026-04-27
future: true
htmlwidgets: true

authors:
  - name: Anonymous

bibliography: 2026-04-27-industrial-agent-benchmarking.bib

toc:
  - name: Introduction
  - name: Why Industrial Agent Evaluation Needs a Rethink
  - name: AssetOpsBench: A Foundation for Real-World Agent Evaluation
    subsections:
      - name: Data Modalities and Scenario Design
      - name: The Six-Dimensional Evaluation Framework
  - name: Why Codabench Was Necessary
  - name: Building AssetOpsBench-Live: From Offline Benchmarks to Real-Time Evaluation
    subsections:
      - name: Pipeline Architecture
      - name: Hidden Scenarios and Privacy Constraints
      - name: Methodology for Selecting Development vs Testing Instances
  - name: Community Results: 225 Users, 300+ Agents, One Reality Check
    subsections:
      - name: Leaderboard Behavior
      - name: Observed Failure Patterns
      - name: Emergent Failure Modes
  - name: Case Study: Missing Clarifying Questions and Its Impact
  - name: What We Learned About Industrial Agents
  - name: Implications for Agentic AI
  - name: Future Directions
  - name: Conclusion
---

# Introduction

Large Language Model (LLM) agents have quickly become the default way researchers test “autonomous” reasoning, planning, and multi-step tool use. Yet almost all widely-used benchmarks evaluate tasks that are far removed from real-world industrial systems — web navigation, coding tasks, synthetic puzzles, or curated IT workflows. In practice, industrial operations demand something more difficult: **multi-agent coordination over heterogeneous telemetry, failure modes, work orders, and safety-critical tasks — all under privacy constraints.**

To understand whether current agents can survive this environment, we built a two-part evaluation ecosystem:

1. **AssetOpsBench** — a multi-modal industrial agent benchmark grounded in real sensor telemetry, failure models, and maintenance workflows :contentReference[oaicite:0]{index=0}  
2. **AssetOpsBench-Live** — a real-time, privacy-preserving, Codabench-powered competition platform we deployed to 225+ users, yielding over 300 code-submitted agent evaluations :contentReference[oaicite:1]{index=1}  

This blog post presents what we learned after systematically evaluating LLM agents — not via prompt logs, but through **real code, real trajectories, and real industrial tasks**.

The outcome is blunt: **current agentic AI is far less reliable than leaderboard scores suggest.** The challenge surfaces failure modes that traditional agent benchmarks simply never expose.

---

# Why Industrial Agent Evaluation Needs a Rethink

Industrial tasks do not behave like web navigation or software debugging. A single query may require:

- retrieving time-series telemetry  
- correlating it with failure models  
- understanding historical work orders  
- invoking specialized analytic tools  
- generating explanations or interventions  
- coordinating with multiple sub-agents  

Existing agent benchmarks (AgentBench, MultiAgentBench, MLE-Bench) do not include these characteristics. They evaluate agents on clean, single-modality tasks with minimal domain constraints. The result is misleading optimism.

Real industrial agent evaluation requires:

- **multi-agent sequences**, not single policies  
- **ambiguity, missing data, and drift**, not curated inputs  
- **tool invocation correctness**, not text-only reasoning  
- **ground-truth procedural forms**, not free-form answers  
- **privacy-preserving execution**, not public trajectories  

AssetOpsBench was designed specifically to fill this gap.

---

# AssetOpsBench: A Foundation for Real-World Agent Evaluation

AssetOpsBench is the first benchmark built around **authentic industrial asset operations** — chillers, AHUs, IoT telemetry, work orders, FMEA data, and domain-authored task scenarios. It contains:

- **2.3M sensor telemetry points**  
- **4.2K work orders**  
- **53 structured failure modes**  
- **140+ curated scenarios across 4 agent classes**  
- **both single-agent and multi-agent workflows**  

It evaluates not whether the agent “responds reasonably,” but whether it follows the **right sequence**, uses the **correct tools**, retrieves the **correct data**, and produces **verifiable results**.

## Data Modalities and Scenario Design

Tasks are grounded in real operational needs:

- anomaly detection  
- failure mode reasoning  
- KPI forecasting  
- sensor–failure alignment  
- work order summarization  
- multi-agent end-to-end fault analysis  

Scenarios include metadata such as task type, expected output form, operational category, and required sub-agents.

This explicit structuring allows *procedural* evaluation instead of subjective grading.

## The Six-Dimensional Evaluation Framework

Every agent run is scored along six axes:

1. **Task completeness**  
2. **Data retrieval accuracy**  
3. **Result verification**  
4. **Action sequence correctness**  
5. **Clarity and justification**  
6. **Hallucination rate**  

These metrics are essential because agents routinely “sound correct” without doing the correct work — a recurring theme we quantify later.

---

# Why Codabench Platform Was Necessary for Evaluation

Benchmarks alone do not solve the fundamental issues with evaluating industrial agents:

- **Data privacy** blocks public scenario sharing  
- **Models often need code execution**, not text-only reasoning  
- Competitions that only return scores **stifle learning and increase dropout**  
- Industrial environments require **reproducibility, versioning, and containerization**

Codabench solves these operational and research constraints:

- Submissions are **Dockerized**  
- Evaluation runs on **hidden scenarios**  
- Trajectories never leave the secure environment  
- Only final **scores + failure clusters** are returned  
- Multi-track design supports both **Planning** and **Execution** agents  

This transforms the benchmark into a **community-scale evaluation ecosystem**.

---

# Building AssetOpsBench-Live: From Offline Benchmarks to Real-Time Evaluation

AssetOpsBench-Live extends AssetOpsBench into an online, reproducible competition environment.

## Pipeline Architecture

The full pipeline (see system diagram on page 2 of the AAAI demo paper) includes:

1. **Local simulated environment** (CouchDB, reference agents, telemetry, alerts, failure modes)  
2. **Containerized agent submission** (1 .py + 1 .json)  
3. **Submission pre-validation**  
4. **Execution on public and hidden scenarios**  
5. **Evaluation using LLM-as-Judge with six metrics**  
6. **Automated failure-mode discovery**  
7. **Return of aggregated scores and clustered failures**  

This pipeline allows anyone to evaluate their agent realistically without accessing private data.

## Hidden Scenarios and Privacy Constraints

Industrial data cannot be public.  
Codabench enables:

- Running code inside a locked environment  
- Evaluating on scenarios that participants never see  
- Returning only structured scores and failure summaries  
- Ensuring deterministic, reproducible runs  

This was essential for wide community participation.

## Methodology for Selecting Development vs Testing Instances

We partitioned scenarios into:

- **development set**: accessible via local simulator  
- **hidden test set**: exclusively used in Codabench evaluation

Instance selection followed three principles:

1. **Coverage** across IoT, TSFM, FMSR, WO, and multi-agent workflows  
2. **Difficulty stratification** based on known historical failure rates  
3. **Drift, missing data, and rare-event inclusion** to reflect real industrial dynamics  

This prevents overfitting and encourages robust reasoning strategies.

---

# Community Results: 225 Users, 300+ Agents, One Reality Check

Over the course of the evaluation:

- **225+ users** participated  
- **300+ containerized agent submissions** were executed  
- Across **two tracks** (Planning, Execution)  
- Using **dozens of custom agent architectures** and coding styles  

This is one of the largest-scale industrial agent evaluations to date.

## Leaderboard Behavior

We evaluated leading frontier and open-source models:

- gpt-4.1  
- mistral-large  
- llama-4-maverick  
- llama-4-scout  
- llama-3-70b  
- granite-3-8b  

Key observations:

- Tool-as-Agent significantly outperformed Plan-Execute in correctness  
- Frontier models led, but none surpassed reliability thresholds required for industrial deployment  
- Smaller models collapsed under multi-step reasoning and retrieval constraints  

## Observed Failure Patterns

Across **881 recorded trajectories** (from AssetOpsBench), failure categories revealed systemic weaknesses:

- **31.2%** — ineffective error recovery  
- **23.8%** — overstated task completion  
- **21.4%** — ambiguous or extraneous formatting  
- **10.3%** — unhandled tool errors  
- **8%** — failure to incorporate feedback  

These patterns also appeared consistently in the Codabench evaluations.

## Emergent Failure Modes

Beyond the 14 predefined failure modes, agents produced:

- **185 traces with one new failure mode**  
- **164 traces with two new emergent failures**  

Semantic clustering revealed 6 canonical emergent categories, including:

- incorrect error handling  
- invalid action formatting  
- mistaken internal state assumptions  
- compound failures involving multiple sub-agents  

This underscores the need for *adaptive* taxonomies as agent complexity increases.

---

# Case Study: Missing Clarifying Questions and Its Impact

One concrete insight from the challenge:

> Nearly 10% of all failures traced back to **not asking clarifying questions** when required.

Enabling clarifying-question behavior (`ask=True`) produced dramatic improvements:

| Model | Before | After |  
|-------|--------|--------|  
| LLaMA-4 Maverick | 59% | **66%** |  
| LLaMA-3-405B | 44% | **61%** |  

This demonstrates the value of targeted diagnostics — participants used feedback to improve models, not guesses.

---

# What We Learned About Industrial Agents

From both benchmark and competition data, several conclusions are unavoidable:

1. **Agents frequently hallucinate successful completion without performing required steps.**  
2. **Correct tool invocation is much harder than textual reasoning.**  
3. **In-context examples are necessary for 80%+ of successful runs.**  
4. **Plan-Execute agents over-plan even simple tasks.**  
5. **Tool-As-Agent is more accurate but slower — a practical deployment tradeoff.**  
6. **Evaluation must consider multi-agent coordination, not just single-turn quality.**  
7. **Human-like ambiguity (missing data, drift, inconsistent logs) drastically increases failure rates.**

These insights do not appear in synthetic agent benchmarks — only in real industrial workflows.

---

# Implications for Agentic AI

Our results suggest several broader implications:

- **Current agentic architectures are not ready for safety-critical industrial automation.**  
- **Benchmarks need real operational context**, not sanitized tasks.  
- **Failure-mode discovery should be a built-in component** of agent evaluation.  
- **Competitions accelerate agent improvement more than paper benchmarks alone.**  
- **Privacy-aware evaluation is necessary for industry adoption.**

Industrial AI cannot rely on prompt engineering and small-scale toy tasks.  
We need rigorous, reproducible, scenario-grounded evaluation pipelines.

---

# Future Directions

Building on this work, we plan to:

- incorporate compute-cost constraints  
- add real-time latency and deadline requirements  
- expand to new industries (steel, energy, data centers)  
- introduce richer multi-agent orchestration tasks  
- develop open leaderboards with long-horizon evaluations  
- evaluate agent reward models and long-term memory systems  

These extensions aim to push agentic AI toward real deployability.

---

# Conclusion

Industrial environments expose weaknesses in LLM agents that traditional benchmarks never reveal.  
By combining AssetOpsBench with a Codabench-powered real-time pipeline, we created a community-scale, privacy-preserving benchmark that evaluates agents not on their *style*, but on their *actions*.

The results are unequivocal:

**Agentic AI is promising — but not yet reliable for real-world industrial operations.  
Robust evaluation is the missing link, and community-driven benchmarks are the path forward.**

