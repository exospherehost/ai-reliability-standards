# AIRE Principles

*Guiding tenets for AI Reliability Engineering, inspired by SRE.*

These five principles define the philosophical foundation of AIRE. They inform the practices detailed in the five pillars and help teams make trade-off decisions when designing reliable AI systems.

---

## 1. Embrace Non-Determinism

> *Accept that identical inputs will produce variable outputs. Design systems that succeed despite variance, not systems that assume consistency.*

### What It Means

Traditional software is deterministic: same input → same output, always. AI systems are probabilistic: same input → different outputs across runs. This is not a bug - it's inherent to how LLMs work.

**Bad Response:** "We need to make the AI deterministic by lowering temperature to 0 and using seed values."

**Good Response:** "We accept variance. We'll use structured outputs to constrain the format and guardrails to enforce constraints."

### When to Apply

- **Designing fallback paths:** Expect primary system to fail non-deterministically → need backup plans
- **Testing:** Use semantic similarity matching (not exact string matching)
- **Monitoring:** Track distributions (P50, P95 latency), not single values

### Anti-Patterns

- ❌ Expecting 100% consistency in outputs
- ❌ Over-tuning prompts to eliminate all variance
- ❌ Treating test failures as deterministic bugs

### Related Patterns

- **[Pillar 1: Resilient Architecture](./pillars/resilient-architecture.md)** - Fallback paths, circuit breakers
- **[Pillar 4: Security](./pillars/security.md)** - Adversarial robustness through guardrails

---

## 2. Reliability is a Feature

> *Reliability competes with velocity for engineering resources. Treat it as a first-class product requirement with explicit budgets, not an afterthought.*

### What It Means

Reliability is not "free" - it requires engineering time, infrastructure cost, and operational overhead. Teams must explicitly budget for reliability work (golden datasets, observability, guardrails) alongside feature work.

**Bad Response:** "We'll worry about reliability after we launch."

**Good Response:** "We allocate 20% of each sprint to reliability work: golden dataset updates, eval pipeline maintenance, incident reviews."

### When to Apply

- **Sprint planning:** Dedicate sprint capacity to reliability tasks
- **Roadmap prioritization:** Reliability features (evals, guardrails) compete with product features
- **Hiring:** Hire for reliability skills (observability, testing, incident response)

### Anti-Patterns

- ❌ "We'll add tests later"
- ❌ "Reliability is the ops team's problem"
- ❌ Ignoring tech debt from fast-moving prototypes

### Related Patterns

- **[Pillar 3: Quality & Lifecycle](./pillars/quality-lifecycle.md)** - Evals-driven deployments, feedback loops

---

## 3. Measure, Don't Assume

> *If you cannot quantify the reliability of your AI system, you do not have a reliable AI system. Intuition is not evidence.*

### What It Means

"Vibes-based" development ("it feels like it's working") is not acceptable. Reliability must be measured with concrete metrics: hallucination rate, HITL rate, uptime, MTTR.

**Bad Response:** "Our agent seems pretty good. Users aren't complaining much."

**Good Response:** "Our hallucination rate is 0.08% (measured on 500 samples). HITL rate is 12%. We're tracking toward <10%."

### When to Apply

- **Deployment decisions:** Block deployments if metrics degrade (accuracy drops >5%)
- **Model selection:** Choose models based on measured performance, not marketing claims
- **Incident response:** Use metrics to diagnose root causes (latency P95 spiked = circuit breaker opened)

### Anti-Patterns

- ❌ Relying on "spot checks" instead of golden datasets
- ❌ Shipping without baseline metrics
- ❌ Ignoring drift because "it looks fine"

### Related Patterns

- **[Pillar 2: Cognitive Reliability](./pillars/cognitive-reliability.md)** - Hallucination rate, groundedness score
- **[Pillar 3: Quality & Lifecycle](./pillars/quality-lifecycle.md)** - Golden datasets, offline/online evals

---

## 4. Fail Gracefully, Fail Informatively

> *Every failure should preserve context, enable recovery, and generate learnings. Silent failures are unacceptable.*

### What It Means

Failures will happen (see Principle 1: Embrace Non-Determinism). The question is: does your system handle failures gracefully, or does it crash spectacularly?

- **Graceful failure:** Circuit breaker opens → fallback to GPT-3.5 → user gets degraded but working response
- **Spectacular failure:** LLM API timeout → agent crashes → user sees 500 error

**Informative failure:** Log reasoning, state, and context so you can debug later.

**Bad Response:** Agent crashes with no logs. No idea what went wrong.

**Good Response:** Agent saves checkpoint, logs error with full context (user query, reasoning, state), returns user-friendly error message, escalates to human.

### When to Apply

- **State management:** Save checkpoints so workflows can resume after crash
- **Logging:** Log Chain of Thought reasoning, not just inputs/outputs
- **User experience:** Never show raw LLM errors - translate to user-friendly messages

### Anti-Patterns

- ❌ Silent failures (logs say "success" but output is garbage)
- ❌ Stateless agents (crash = restart from Step 1)
- ❌ No audit trail (can't investigate incidents)

### Related Patterns

- **[Pillar 1: Resilient Architecture](./pillars/resilient-architecture.md)** - State management, circuit breakers, fallback paths
- **[Pillar 3: Quality & Lifecycle](./pillars/quality-lifecycle.md)** - Chain of Thought logging

---

## 5. Humans as Fallback, Not Crutch

> *Design for autonomous operation. Human escalation is a safety net for edge cases, not a substitute for robust engineering.*

### What It Means

HITL (Human-in-the-Loop) is essential for high-stakes decisions, but if 50% of requests need human review, your system isn't working. The goal is to **reduce HITL over time** through active learning.

**Bad Response:** "When the agent isn't sure, we just ask a human."

**Good Response:** "HITL rate started at 30%. After 3 months of active learning (adding HITL corrections to golden dataset), we're down to 8%."

### When to Apply

- **Confidence thresholds:** Only escalate to humans when confidence <0.7
- **Staged rollout:** Start with 100% HITL, reduce to 10% over time
- **Active learning:** Use HITL corrections to retrain models

### Anti-Patterns

- ❌ HITL as default (agent always asks human before acting)
- ❌ No confidence calibration (agent doesn't know when it's unsure)
- ❌ Ignoring HITL feedback (corrections not added to golden dataset)

### Related Patterns

- **[Pillar 2: Cognitive Reliability](./pillars/cognitive-reliability.md)** - HITL protocols, confidence calibration
- **[Pillar 3: Quality & Lifecycle](./pillars/quality-lifecycle.md)** - Feedback loops, golden dataset updates

---

## Applying the Principles

These principles are not rules - they're guides for making trade-off decisions. When facing a design choice, ask:

1. **Embrace Non-Determinism:** Am I designing for consistency or resilience?
2. **Reliability is a Feature:** How much engineering time am I allocating to reliability?
3. **Measure, Don't Assume:** What metrics will I use to validate this works?
4. **Fail Gracefully:** What happens when this component fails?
5. **Humans as Fallback:** Can this system improve over time without human intervention?

---

## Example: Designing a Refund Agent

Let's apply all 5 principles to designing a customer refund agent:

### Scenario
Build an agent that processes refund requests automatically.

### Design Decisions

| Principle | Application |
|-----------|-------------|
| **1. Embrace Non-Determinism** | Agent may extract different refund reasons from same query → Use structured outputs to constrain format |
| **2. Reliability is a Feature** | Allocate 2 weeks for golden dataset (100 refund examples), 1 week for guardrails |
| **3. Measure, Don't Assume** | Track: Refund approval accuracy (>95%), HITL rate (<10%), Fraud detection rate |
| **4. Fail Gracefully** | If fraud detection fails → escalate to human with full context (not crash) |
| **5. Humans as Fallback** | Start with HITL for all refunds >$100. After 1 month, reduce threshold to >$500 using active learning |

**Result:** System that's reliable, measurable, and improves over time.

---

*These principles are part of the [AI Reliability Engineering (AIRE) Standards](./index.md). Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).*
