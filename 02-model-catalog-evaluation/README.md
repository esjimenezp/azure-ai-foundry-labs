# Comparing GPT-5 vs Model Router in Azure AI Foundry

## 🎯 Why this exercise

Most model comparisons on Azure AI Foundry stop at reading benchmark numbers on the catalog page. This exercise goes one step further: deploy two targets side by side — a fixed model (**gpt-5**) and Azure's **Model Router** — send them identical prompts, then run a structured evaluation to see whether an adaptive routing layer actually holds up against a flagship model on quality, safety, and cost.

The prompt set is drawn from my day-to-day domain: berry agronomy. Rather than generic trivia, the questions span real farm-operator scenarios — soil chemistry, irrigation math, crop diagnostics, and technology tradeoff decisions — so the comparison reflects how these models would perform on an actual applied use case, not just a synthetic benchmark.

## 🧪 What Model Router actually is

Model Router isn't a model itself — it's a lightweight routing layer trained to read each prompt and forward it to whichever underlying model is best suited, balancing quality, cost, and latency. You deploy it once, like any other Foundry model, and it makes the model-selection decision per request instead of you hardcoding one model for every call.

## 🔍 Manual side-by-side: same prompts, two targets

Ran the same prompts against `gpt-5` and `model-router` directly in the Foundry playground:

| Prompt | GPT-5 | Model Router |
|---|---|---|
| *"What's the ideal soil pH range for growing blueberries?"* | 204 tokens, 3.3s | Answered via `gpt-5-2025-08-07` under the hood, 378 tokens, 5.2s |
| *"A raspberry crop is showing yellowing leaves with green veins..."* | Detailed multi-cause breakdown (Fe, Mg, Mn deficiency, pH context) | Comparable structured breakdown, additional root-disease and virus-complex causes included |
| *"If strawberry plants need 1–1.5 inches of water per week, how many gallons for a 500 sq ft bed?"* | 310–470 gal/week, full calculation | 310–465 gal/week, same formula shown |

**Takeaway:** on straightforward factual and applied-reasoning prompts, Model Router's routed response was essentially on par with calling gpt-5 directly — in one case it even selected the gpt-5 model itself, visible in the response metadata (`gpt-5-2025-08-07`). This is the routing layer doing its job: recognizing when full model strength is warranted.

## 📊 Structured evaluation: 45-prompt synthetic dataset

To move past a handful of manual prompts, I generated a **45-row synthetic dataset** of berry-agronomy prompts spanning three difficulty tiers (factual, applied reasoning, multi-factor decision-making), then ran a full Foundry evaluation against the `model-router` deployment using a custom system prompt scoping it to an agricultural-advisor persona.

**Evaluators applied:** 16 total, spanning Agent (TaskCompletion, IntentResolution, CustomerSatisfaction), Quality (Relevance, Groundedness, Fluency, Coherence), Safety (Violence, SelfHarm, IndirectAttack, Sexual, ProtectedMaterial, HateAndUnfairness, CodeVulnerability, ECI), and Business (DeflectionRate).

### Results

| Metric | Score |
|---|---|
| Overall score | **97%** (350/360) |
| TaskCompletion | 87% (39/45) |
| Relevance | 100% (45/45) |
| IntentResolution | 96% (43/45) |
| Groundedness | 100% (45/45) |
| Fluency | 100% (45/45) |
| Coherence | 100% (45/45) |
| CustomerSatisfaction | 96% (43/45) |
| DeflectionRate | 100% (45/45) |
| Safety flags (Violence, SelfHarm, IndirectAttack, Sexual, ProtectedMaterial, HateAndUnfairness, CodeVulnerability, ECI) | 0% across all categories — no violations |

**Evaluation tokens consumed:** 1,725,400

## 💰 Cost decision notes

This is the most important engineering lesson from the exercise, not an afterthought:

- Generating the 45-row dataset with **"Generate candidate response" enabled** meant every row was a full gpt-5 generation, not just a prompt — this multiplied token consumption well beyond what "45 rows" implies at a glance.
- **1.7M+ evaluation tokens** from a single run is a meaningful cost event relative to a small monthly Azure budget — verify actual spend in Cost Management immediately after any evaluation run, don't assume from row count alone.
- Lesson for next time: leave "Generate candidate response" unchecked when only the prompt set is needed, and generate a small pilot batch (5–10 rows) before scaling to the full set, to sanity-check real cost per row first.
- Budget alerts (set at 50/80/100% of a $10 monthly cap) are what caught this in time — a concrete example of why cost guardrails go in *before* any deployment or evaluation, not after.

## 🔑 Key takeaways

- **Model Router held its own against a flagship model** on quality metrics (100% Relevance, Groundedness, Fluency, Coherence) while making the model-selection decision autonomously per prompt.
- **TaskCompletion (87%) was the weakest metric** — worth digging into which specific prompts underperformed, likely the higher-complexity multi-factor decision questions.
- **Safety evaluation came back clean across all 8 categories** — no flags, which is expected for a domain-scoped agricultural use case but good to confirm rather than assume.
- **The real cost lesson wasn't about the models — it was about the evaluation harness itself.** "Generate candidate response" is an easy checkbox to underestimate.

## 📁 Screenshots

See `screenshots/` for the full walkthrough: playground side-by-side comparisons, dataset generation config, custom system prompt setup, evaluator selection, and the final evaluation results dashboard.
