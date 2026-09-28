# Preset: budget (cost-optimized)

Haiku everywhere except where Sonnet is the floor. Roughly 5-10x cheaper than balanced per dispatch.

| Agent | Model | Reason |
|-------|-------|--------|
| explorer | haiku | Cheapest retrieval |
| librarian | haiku | Summarized docs only |
| oracle | sonnet | Cheapest acceptable reviewer |
| designer | haiku | Mechanical styling; expect more iterations |
| fixer | haiku | Small well-scoped edits |
| backend | haiku | Simple CRUD only; route complex data work to sonnet tier |
| tester | haiku | Cheap suites; expect weaker edge-case hunting |
| safeguard | sonnet | Cheapest acceptable auditor |
| observer | haiku | Cheapest vision |
| councillor seats | alpha=sonnet, beta=haiku, gamma=haiku | One strong seat + two cheap |

Tradeoffs: weaker design output, shallower review, more loop iterations. Use for bulk or low-stakes work. Applies at dispatch time (see /preset).