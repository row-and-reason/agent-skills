# Row & Reason Agent Skills

Agent Skills for Row & Reason products: research and reusable web data with Periplus, and labeling tabular data with AI with rowlab.

| Skill | Use |
| --- | --- |
| `periplus-dataset-research` | Build a defensible dataset to answer a research question |
| `periplus-data-extraction` | Turn known pages into validated records |
| `periplus-change-monitoring` | Compare observations and set up recurring acquisition |
| `periplus-multi-source-enrichment` | Add evidence-backed fields while resolving entity matches |
| `rowlab-labeling` | Add a column that says something about every row: classify, extract, summarise, translate |
| `rowlab-model-selection` | Choose a model, or measure how good a labelled column is |
| `rowlab-labeling-at-scale` | Label large datasets without paying for calls the question doesn't need |

These skills supply methodology. Connect the MCP server separately: Periplus with the [live MCP guide](https://docs.periplus.dev/mcp), rowlab with the MCP section of its [README](https://github.com/row-and-reason/rowlab#mcp). Use other tools where they better fit the task. No credentials are bundled.

List the available skills, then install just the ones you want:

```sh
npx skills add row-and-reason/agent-skills --list
npx skills add row-and-reason/agent-skills --skill periplus-dataset-research
```

Replace the skill name with any name in the table. Add `-a codex -g` to install globally for Codex.

The independently installable folders are under `skills/`. Installing a skill supplies instructions; it does not connect or sign in to the MCP server.
