# Preset: openai (cross-provider via gateway)

Mixes OpenAI models through a gateway exposed to Claude Code. Valid only if a proxy (LiteLLM, claude-code-router, or similar) is configured so these model IDs resolve in the Agent tool. Without a gateway this preset cannot dispatch.

| Agent | Model | Reason |
|-------|-------|--------|
| explorer | gpt-4.1-mini | Fast retrieval |
| librarian | gpt-4.1 | Web research depth |
| oracle | o3 | Strong reasoning for review |
| designer | gpt-4.1 | Balanced creative work |
| fixer | gpt-4.1-mini | Cheap implementation |
| backend | gpt-4.1 | Schema and query reasoning |
| tester | gpt-4.1-mini | Cheap verification |
| safeguard | o3 | Deep threat analysis |
| observer | gpt-4.1-mini | Vision-capable extraction |
| councillor seats | alpha=o3, beta=gpt-4.1, gamma=gpt-4.1-mini | Model diversity across tiers |

Model IDs must match what the gateway exposes. Adjust the table to the proxy's actual names. Applies at dispatch time (see /preset).