# me-alz-tf

CAF-aligned Azure landing zone in Terraform: platform and application landing zones, extended with the AI Landing Zone for AI workloads.

> **Status:** scaffold only. The folders and files exist but nothing is wired up yet: no modules, providers or backends.

## Layout

| Path | Purpose |
|---|---|
| [bootstrap/](bootstrap/) | One-time setup: Terraform state storage, pipeline identities (OIDC), environments, RBAC. Starts on local state. |
| [lib/](lib/) | ALZ library overrides: management group architecture, archetypes (including `corp_ai` / `online_ai`), custom policy definitions, sets and assignments. |
| [platform/governance/](platform/governance/) | Management groups and policy. |
| [platform/management/](platform/management/) | Log Analytics, Azure Monitor Agent, data collection rules, automation. |
| [platform/connectivity/](platform/connectivity/) | Hub-spoke or Virtual WAN, firewall, DNS private resolver, private DNS zones (including AI service zones). |
| [platform/identity/](platform/identity/) | Identity subscription resources. |
| [platform/ai-shared/](platform/ai-shared/) | Optional shared AI services: APIM AI gateway, central Azure OpenAI / Foundry capacity. |
| [landing-zones/](landing-zones/) | Subscription vending. Each landing zone is a request file under `requests/{corp,online,ai}/`. |
| [workloads/](workloads/) | Workload deployments into vended subscriptions. AI workloads (e.g. [workloads/ai/chatbot/](workloads/ai/chatbot/)) deploy the AI Landing Zone. |
| [modules/](modules/) | Local modules: thin wrappers and composites only. Prefer Azure Verified Modules directly. |
| [environments/](environments/) | Non-secret per-environment configuration (`prod`, `nonprod`). |
| [docs/](docs/) | [Architecture](docs/architecture.md) and [architecture decision records](docs/adr/). |
| [.github/workflows/](.github/workflows/) | One pipeline per layer. |

## Layers and deployment order

Each layer is its own Terraform root with its own state. Dependencies only flow downward:

1. `bootstrap`
2. `platform/governance`
3. `platform/management`
4. `platform/connectivity` and `platform/identity`
5. `platform/ai-shared` (optional)
6. `landing-zones`
7. `workloads/*`

A layer reads what it needs from layers above it (remote state, data sources or pipeline outputs). It never writes to them.

## AI Landing Zone extension

- **Governance:** `corp_ai` / `online_ai` archetypes under Landing Zones, with AI-specific policy such as no public network access, private endpoints required and local auth disabled.
- **Connectivity:** private DNS zones and firewall rules for AI services live in the platform hub.
- **Vending:** AI subscriptions are requested under `landing-zones/requests/ai/`.
- **Workloads:** each AI workload deploys the AI Landing Zone into its own spoke subscription. It uses platform DNS and egress, and optionally the shared AI gateway.

## Conventions

- Keep secrets out of the repo. Use Key Vault or pipeline secrets.
- `*.tfvars` is currently git-ignored. Narrow that rule before committing per-environment configuration to [environments/](environments/).
- Record significant design choices as an ADR in [docs/adr/](docs/adr/).
