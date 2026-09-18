# vLLM Semantic Router — 30-Minute Presenter Guide

Interactive presentation matching the [live demo format](https://vsr-demo-user-cnuland.apps.ocp.cloud.rhai-tmm.dev/).

## Quick Start

```bash
cd ~/Desktop/Presentation
python3 -m http.server 8080
# Open http://localhost:8080
```

**Controls:**
| Key | Action |
|-----|--------|
| `→` / `Space` | Next slide |
| `←` | Previous slide |
| `P` | Toggle speaker notes panel |
| `T` | Reset 30-minute timer |
| `A` | Auto-play (25s per slide) |
| `F` | Fullscreen |

---

## Slide-by-Slide Script (~30 minutes)

### Slide 1: Introduction (~1.5 min)
**Scene:** Title — "Smarter Routing for Safer AI"

> Welcome everyone. Today we're walking through **vLLM Semantic Router** — an open-source, content-aware routing layer for enterprise LLM inference.
>
> The problem we're solving: organizations now run **multiple models** — local, cloud, specialized — but their infrastructure routes requests blindly. Semantic Router makes the routing layer **understand what each request contains** and **where it should go**.
>
> We'll cover architecture, three real-world use cases, and integration with the **llm-d** inference stack on Kubernetes.

---

### Slide 2: The Multi-Model Challenge (~2.5 min)
**Scene:** Four pain-point cards

> Let me set up the problem with four pain points enterprises hit in production:
>
> 1. **Unnecessary spend** — simple lookups hitting $10/1M cloud models
> 2. **Data sovereignty** — PII and financial data leaving the data center
> 3. **Safety gaps** — jailbreaks passing straight to inference
> 4. **Latency sprawl** — wrong model for the wrong task
>
> **The core problem:** today's pipelines are content-blind. Load balancers see HTTP headers, not semantics.
>
> As gen AI moves from prototypes to production, this gap becomes critical.

---

### Slide 3: The Solution (~2 min)
**Scene:** Core capabilities + design principles

> vLLM Semantic Router is a **request classifier** that sits in front of your LLM backends — system-level intelligence for mixture-of-models.
>
> Four core capabilities:
> - **Semantic classification** — understands intent and complexity from content
> - **Policy-driven routing** — signal-decision rules with configurable priority
> - **Safety-aware signals** — jailbreak, PII, hallucination detection as routing inputs
> - **Semantic caching** — HNSW vector similarity beyond exact-match
>
> Key principle: **zero code changes**. Clients send `model: "auto"`. The router handles everything.

---

### Slide 4: Architecture (~2.5 min)
**Scene:** Pipeline diagram — Client → Envoy → ExtProc → Backends

> Here's the architecture. Requests flow:
>
> **Client** (OpenAI SDK) → **Envoy Proxy** (port 8899) → **ExtProc** (Go router + ML classifiers) → **Backend models** (vLLM or cloud)
>
> Three subsystems inside the router:
> - **Classification engine** — mmBERT-32K, 307M params, 1,800+ languages
> - **Signal-decision architecture** — Boolean expression trees with priority
> - **Semantic cache** — paraphrased duplicate detection
>
> Envoy's External Processing filter intercepts each request via gRPC. Completely transparent to clients.

---

### Slide 5: Signals & Decisions (~3 min)
**Scene:** Signal types → Decision rules with priorities

> This is the routing brain. Four signal types feed decision rules:
>
> 1. **Keyword matching** — fast surface classification
> 2. **Embedding similarity** — catches paraphrased intent
> 3. **Neural classifiers** — domain, complexity, safety
> 4. **Domain classification** — auto-detect math, code, medical, legal
>
> Decisions evaluate **highest priority first**:
> - P100: Reasoning → Cloud LLM
> - P90: Jailbreak/PII → Isolated model or block
> - P80: Coding → Code specialist
> - P1: Default → Local LLM
>
> Every response includes `x-vsr-*` headers for full observability.

---

### Slide 6: Cost Optimization (~2.5 min)
**Scene:** Request routing animation + metrics

> First use case: **cost-optimized routing**.
>
> Watch how simple queries route to free local models while complex reasoning goes to cloud.
>
> Real numbers from telecom/enterprise workloads:
> - **86%** of requests routed local
> - **40ms** P50 routing latency
> - **0.4%** overhead vs inference
>
> But accuracy matters — pretrained embeddings misroute 1 in 5 requests. That's where fine-tuning comes in.

---

### Slide 7: Fine-Tuning (~2.5 min)
**Scene:** Before/after accuracy comparison

> The pretrained model (all-MiniLM-L6-v2) achieves only **80.4%** accuracy on 4-tier complexity classification.
>
> We fine-tuned with:
> - 805 synthetic examples from 48 seed anchors
> - 6 data generation strategies
> - **BatchAllTripletLoss** with GROUP_BY_LABEL sampling
>
> Result: **98.5% accuracy** — only 3 errors in 204 tests.
>
> The model fine-tunes in under 2 hours on CPU and drops in as a config swap. No architecture changes.

---

### Slide 8: Fine-Tuning Pipeline (~2 min)
**Scene:** Red Hat AI pipeline diagram

> On Red Hat AI / OpenShift, the full pipeline is automated:
>
> Seed anchors → Synthetic data generation (Kimi K2.6) → Verification (Qwen 3.6) → Training (BatchAllTripletLoss) → Evaluation (MLFlow) → Deploy to router
>
> Generator and verifier run on NVIDIA H200 nodes via KServe + vLLM with Kueue GPU scheduling.
>
> From seeds to production model — one platform, fully automated.

---

### Slide 9: Data Sovereignty (~2.5 min)
**Scene:** On-prem vs cloud zones + sensitivity ranking

> Second use case: **banking and finance data sovereignty**.
>
> On-premise: sovereign LLM for PII, financial data, regulated queries.
> Cloud: general-purpose for non-sensitive research and knowledge tasks.
>
> **Data sensitivity ranking:** Critical → High → Medium → Low, each with routing policies.
>
> But routing alone isn't enough. **Defense in depth:**
> 1. Semantic routing (majority of cases)
> 2. Eval & guardrails (catches misclassifications)
> 3. Egress controls (final stopgap)

---

### Slide 10: Enterprise Guardrails (~2.5 min)
**Scene:** Four-stage safety pipeline with live examples

> Third use case: **safety-first inference**.
>
> Four stages: Input filtering → Classification → Policy routing → Egress guardrails.
>
> Live examples:
> - Jailbreak attempt → **BLOCKED**
> - SSN in prompt → **PII REDACTED**
> - Safe technical query → **ROUTED**
> - Phishing template request → **BLOCKED**
>
> Athena ships 8 neural classifiers on mmBERT-32K. Safety rules at P90+ fire before any routing decision.

---

### Slide 11: Agentic AI (~2 min)
**Scene:** Agent orchestrator with per-call routing

> Why agents need semantic routing: a single agent task generates **10-50+ LLM calls**.
>
> Without routing, every call hits the same expensive model. With routing:
> - Simple lookups → free local 8B model
> - Code generation → code specialist
> - Complex reasoning → cloud LLM
>
> Agents send `model: "auto"`. **80%+ cost reduction** with zero SDK changes.

---

### Slide 12: llm-d Integration (~2 min)
**Scene:** Two-layer stack comparison

> Two layers, one stack:
> - **Semantic Router** decides **which** model (the "what")
> - **llm-d** optimizes **how** that model runs (the "how")
>
> llm-d provides KV-cache-aware scheduling, prefix-hash routing, and tiered memory offloading.
>
> Kubernetes-native: IntelligentPool CRD, IntelligentRoute CRD, Helm + HPA. OpenShift ready.

---

### Slide 13: End-to-End Flow (~2.5 min)
**Scene:** Full request lifecycle animation

> Let's trace a complete request lifecycle:
>
> - **Non-sensitive:** "Summarize this paper" → Router → SaaS LLM
> - **Sensitive:** "Analyze customer portfolio" → Router → llm-d (KV-cache hit) → Egress validation
> - **Egress blocked:** Local model leaks PII → redacted before delivery
>
> Routing covers ingress. Guardrails cover egress. Together: no sensitive data escapes.

---

### Slide 14: Key Takeaways (~1.5 min)
**Scene:** Six metric cards

> Six numbers to remember:
> - **86%** local routing
> - **98.5%** fine-tuned accuracy
> - **40ms** routing latency
> - **10+** routing classifiers
> - **3** defense layers
> - **0** code changes

---

### Slide 15: Resources & Q&A (~1 min)
**Scene:** Link cards

> Resources:
> - [Getting Started Guide](https://developers.redhat.com)
> - [GitHub: vllm-project/semantic-router](https://github.com/vllm-project/semantic-router)
> - [llm-d.ai](https://llm-d.ai)
> - [Fine-tuning pipeline](https://github.com/cnuland/hello-chris-sr-finetuned)
>
> Questions?

---

## Timing Summary

| # | Slide | Duration | Cumulative |
|---|-------|----------|------------|
| 1 | Introduction | 1.5 min | 1:30 |
| 2 | The Challenge | 2.5 min | 4:00 |
| 3 | The Solution | 2 min | 6:00 |
| 4 | Architecture | 2.5 min | 8:30 |
| 5 | Signals & Decisions | 3 min | 11:30 |
| 6 | Cost Optimization | 2.5 min | 14:00 |
| 7 | Fine-Tuning | 2.5 min | 16:30 |
| 8 | FT Pipeline | 2 min | 18:30 |
| 9 | Data Sovereignty | 2.5 min | 21:00 |
| 10 | Guardrails | 2.5 min | 23:30 |
| 11 | Agentic AI | 2 min | 25:30 |
| 12 | llm-d | 2 min | 27:30 |
| 13 | End-to-End | 2.5 min | 30:00 |
| 14 | Summary | 1.5 min | 31:30 |
| 15 | Resources | 1 min | 32:30 |

Slides 14-15 can be compressed if running long. Slides 5, 6, and 13 are the deepest technical content — allocate extra Q&A time there.

## Customizing from Your Google Slides

Your initial deck is at: [Google Slides](https://docs.google.com/presentation/d/1dGu23vhvOVZadug-llq7RnOLd074WVztG8KDQ-kExyU/edit)

To merge your content:
1. Export slides as PDF or copy text per slide
2. Edit the `speakerNotes` array in `index.html` (search for `const speakerNotes`)
3. Modify scene HTML content in the corresponding `scene-*` divs
4. Adjust `sceneDurations` if you add/remove slides

## Deployment

Host on any static file server, OpenShift route, or GitHub Pages:

```bash
# OpenShift example
oc new-app --name vsr-presentation --source . --strategy=source
```
