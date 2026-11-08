# Row & Reason Agent Skills

Agent Skills for Row & Reason products: research and reusable web data with Periplus, and labeling tabular data and measuring label quality with rowlab.

| Skill | Use |
| --- | --- |
| `periplus-dataset-research` | Build a defensible dataset to answer a research question |
| `periplus-data-extraction` | Turn known pages into validated records |
| `periplus-change-monitoring` | Compare observations and set up recurring acquisition |
| `periplus-multi-source-enrichment` | Add evidence-backed fields while resolving entity matches |
| `rowlab` | Label manually or with models, measure quality, choose models and run at scale |

These skills supply methodology. Connect the MCP server separately: Periplus with the [live MCP guide](https://docs.periplus.dev/mcp), rowlab with its [MCP guide](https://docs.rowlab.dev/agents/mcp/). Use other tools where they better fit the task. No credentials are bundled.

List the available skills, then install just the ones you want:

```sh
npx skills add row-and-reason/agent-skills --list
npx skills add row-and-reason/agent-skills --skill rowlab
```

Replace the skill name with any name in the table. Add `-a codex -g` to install globally for Codex.

The independently installable folders are under `skills/`. Installing a skill supplies instructions; it does not connect or sign in to the MCP server.

Rowlab uses one skill. If you installed the earlier three Rowlab skills, remove those installed copies after installing `rowlab`, so agents do not load competing instructions. Manual labeling requires a server exposing `run_answers` and `run_finish`; check the connected server's tool list before using that workflow, whether hosted or self-hosted.

For maintainers publishing this consolidation: first publish the new `rowlab` folder and its install instructions, then deploy the updated Rowlab app and docs, and only then publish removal of the old skill folders. Split additions and deletions across those release steps so live setup commands keep working. The final repository contains only the consolidated Rowlab skill; no compatibility skills are retained.
