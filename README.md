# Row & Reason Agent Skills

Agent Skills for Row & Reason products. The initial collection covers research and reusable web data with Periplus.

| Skill | Use |
| --- | --- |
| `periplus-dataset-research` | Build a defensible dataset to answer a research question |
| `periplus-data-extraction` | Turn known pages into validated records |
| `periplus-change-monitoring` | Compare observations and set up recurring acquisition |
| `periplus-multi-source-enrichment` | Add evidence-backed fields while resolving entity matches |

These skills supply methodology. Connect a Periplus MCP server separately using the [live MCP guide](https://docs.periplus.dev/mcp). Use other tools where they better fit the task. No credentials are bundled.

List the available skills, then install just the ones you want:

```sh
npx skills add Row-Reason/agent-skills --list
npx skills add Row-Reason/agent-skills --skill periplus-dataset-research
```

Replace the skill name with any name in the table. Add `-a codex -g` to install globally for Codex.

The four independently installable folders are under `skills/`. Installing a skill supplies instructions; it does not connect or sign in to the MCP server.
