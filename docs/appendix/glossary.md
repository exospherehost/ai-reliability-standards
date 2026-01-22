# Glossary

Key terms used throughout the AIRE Standards.

---

## A

**Active Learning**
Process where an AI system learns from human corrections (HITL feedback) to improve over time. Corrections are added to the golden dataset and used to retrain the model.

**AIRE**
AI Reliability Engineering. The discipline of building and operating reliable AI agents, inspired by Site Reliability Engineering (SRE).

**Agent**
An autonomous AI system that uses an LLM for reasoning and can take actions (call APIs, query databases, etc.) on behalf of users.

---

## C

**Chain of Thought (CoT)**
The LLM's step-by-step reasoning process before arriving at a final answer. CoT logging captures this reasoning for debugging and observability.

**Circuit Breaker**
Fault tolerance pattern that stops calling a degraded service after repeated failures, preventing cascading failures. Has three states: Closed (normal), Open (failing fast), Half-Open (testing recovery).

**Cognitive Reliability**
The practice of ensuring AI agent outputs are accurate, consistent, and trustworthy through validation, self-reflection, and drift detection.

**Confidence Calibration**
Ensuring an agent's reported confidence score correlates with actual accuracy. A well-calibrated agent with 90% confidence should be correct 90% of the time.

---

## D

**Drift Detection**
Monitoring for changes in input distributions, output characteristics, or model performance over time. Types include input drift, output drift, and model drift.

**Durable Execution**
Workflow execution pattern where state is persisted so workflows can resume from the last checkpoint after failures, rather than restarting from scratch.

---

## E

**EDD (Eval-Driven Development)**
AI equivalent of Test-Driven Development (TDD). Development methodology where golden datasets and evals guide development, and deployments are blocked if evals fail.

**Ephemeral Context**
Context (prompts, conversation history) that is processed but never persisted to logs or storage, used for highly sensitive data like medical records.

---

## F

**Fallback Path**
Alternative strategy when primary system fails. Example: GPT-4 → GPT-3.5 → Rule-based system → Human.

**Feedback Loop**
System where production failures and HITL corrections are collected, added to the golden dataset, and used to retrain models, creating continuous improvement.

---

## G

**Golden Dataset**
Curated collection of inputs with labeled expected outputs, used as a regression suite for evaluating model performance before deployment. Typically 100+ examples.

**Groundedness**
Percentage of claims in an agent's output that are supported by retrieved context or known facts. Target: >95%.

**Guardrails**
Deterministic hard limits that constrain agent behavior regardless of LLM reasoning. Types: input guardrails (PII redaction, prompt injection detection), output guardrails (sensitive data leakage prevention), action guardrails (rate limits, monetary limits).

---

## H

**Hallucination**
When an LLM generates factually incorrect or fabricated information presented as fact.

**Hallucination Rate**
Percentage of agent outputs containing factual errors. Target: <0.1%.

**HITL (Human-in-the-Loop)**
Design pattern where humans review or approve agent actions before execution, typically for high-stakes decisions or low-confidence outputs.

---

## I

**Idempotency Token**
Unique identifier that ensures retrying a failed operation doesn't duplicate side effects (e.g., double-charging a customer).

---

## J

**JIT (Just-in-Time) Privilege Access**
Security pattern where agents are granted minimum necessary privileges scoped to specific actions with automatic expiration (typically <5 minutes).

**Jailbreak**
Attack where adversarial input manipulates the LLM to bypass intended constraints or reveal system prompts.

---

## M

**MTTD (Mean Time to Detect)**
Average time from when an issue occurs to when it's detected. Target: <5 minutes.

**MTTR (Mean Time to Recovery)**
Average time from failure detection to full recovery. Target: <5 minutes.

---

## O

**Offline Evals**
Pre-deployment evaluations run on a golden dataset in CI/CD to catch regressions before they reach production. Cheap and reproducible.

**Online Evals**
Post-deployment evaluations run on production traffic to detect drift and real-world failures. More expensive but catches unknown issues.

---

## P

**Prompt Injection**
Attack where user input contains instructions that override the system prompt, causing the agent to behave incorrectly.

---

## R

**Resumability**
Ability of a workflow to resume from the last checkpoint after a failure, rather than restarting from scratch.

**Rollback**
Reverting to a previous version of a model or system after detecting performance degradation in a new deployment.

---

## S

**Scoped Token**
API key or access token limited to specific actions and resources (e.g., "refundOrder:12345"), reducing blast radius of security breaches.

**Self-Reflection**
Pattern where an LLM critiques its own output before finalizing, useful for high-stakes decisions. Example: Agent generates answer → critiques it → revises answer.

**Staged Rollout**
Deployment strategy where new models are rolled out gradually (5% → 50% → 100% traffic) with monitoring at each stage.

**Structured Output**
LLM output constrained to a specific format (JSON schema, enum) to enable deterministic validation. Example: `{"answer": "...", "confidence": 0.9}`.

---

## T

**Tool**
External function or API that an agent can call to perform actions (e.g., `getWeather()`, `sendEmail()`, `queryDatabase()`).

---

*This glossary is part of the [AI Reliability Engineering (AIRE) Standards](../index.md). Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).*
