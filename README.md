# Azure AI Foundry Labs

Hands-on exercises from the Microsoft Learn course "Develop generative AI solutions in Azure"
([mslearn-ai-studio](https://microsoftlearning.github.io/mslearn-ai-studio/)), documented one
exercise per folder: what I built, what it cost, and what I learned using Microsoft Foundry
in practice.

Portal-driven exercises (project setup, deployment, evaluation, tools) live here. Exercises
that produce a reusable application get their own repo.

## Exercises

| # | Exercise | Status | Notes |
|---|----------|--------|-------|
| 01 | [Explore Foundry](./01-explore-foundry) | Done | Foundry project, `gpt-4.1` deployment, playground, VS Code Foundry Toolkit |
| 02 | [Model catalog and evaluation](./02-model-catalog-evaluation) | Done | Model comparison and a synthetic-data evaluation |
| 03 | Chat app | Done | Built as a standalone repo: [azure-ai-chat-app](https://github.com/esjimenezp/azure-ai-chat-app) |
| 04a | Berry Advisor: tools and grounding | In progress | Agronomy assistant using file search over public extension guides and web search |
| 04b | Fine-tune a language model | Planned | |
| 06 | Guardrails | Planned | |

## How each exercise is documented

Every exercise folder has a README with the goal, the steps, numbered screenshots, a cost
table, and lessons learned, including mistakes.

## Cost control

These exercises run on a personal subscription, so each one follows the same routine: a
dedicated resource group, a budget with alerts, Global Standard deployments with a low
tokens-per-minute cap, and the resource group deleted right after the screenshots are taken.

## Related repos

- [azure-ai-chat-app](https://github.com/esjimenezp/azure-ai-chat-app): async chat client on a Foundry deployment
- [Portfolio](https://esjimenezp.github.io)

## Notes

- Lab code comes from Microsoft's open exercises. See the upstream repo for its license.
- No secrets are committed. Configuration uses `.env` files, which are ignored.
- Source documents used for grounding are linked, never copied.
