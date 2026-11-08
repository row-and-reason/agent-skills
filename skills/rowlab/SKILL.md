---
name: rowlab
description: Label tabular datasets with rowlab, manually or with models; compare label quality, choose models, and run labeling at scale. Use for “label each row”, classification, extraction or text generation, collecting manual labels, “which model should I use”, or “how accurate is this column”.
---

# Rowlab

Define a task, try it on selected rows, measure the quality needed for the user's purpose, then complete and export the run. Model and manual execution use the same task definitions. Each run creates a new dataset containing its selected rows and output columns; it preserves source data. Completed outputs are immutable.

Use the connected MCP tools' descriptions for current parameters and response shapes. The [MCP guide](https://docs.rowlab.dev/agents/mcp/) covers connection and tool usage; the [API reference](https://docs.rowlab.dev/api/) is for scripts. Installing this skill does not connect the server. Check available tools before relying on manual execution; older servers may not expose `run_answers` or `run_finish`.

## Define the task

Import a supplied file with `dataset_add` when it is not already a dataset. Inspect the dataset and representative rows with `dataset_get` and `sql_query`. Establish what one row represents, what the output should mean, and which errors matter. Keep existing reference labels and model predictions in their own columns.

Choose the narrowest useful answer type: yes/no, pick one, pick any, scale, typed extraction, or text. Explain ambiguous options in their descriptions. Add a meaningful “not applicable” option when the task needs one; do not silently invent categories that change the user's question. Build the prompt from the row's actual columns, and check its rendered form with `run_preview`.

Several fields from the same text can share one extraction task. Distinct questions can be separate tasks in one run; grouping tasks does not by itself reduce model calls. Tasks and row selection are frozen when the run starts. Changing them requires a new run, with output names that do not collide with existing columns.

## Choose execution

**Models:** default to `run_start` with `method="llm"` for bulk labeling unless the user requests manual execution or supplies existing labels. Try a small set of rows before an expensive run when the task has not been checked. Read answers alongside their inputs, including empty, ambiguous and unusual cases. A small prompt-debugging sample does not establish accuracy. Decision models are candidates for classification; extraction and generation need language models. Use the live model catalog for capabilities and prices.

Before a paid full run, preview its SQL selection and report rows, calls and estimated cost. If that spend is not covered by existing authorization, agree a budget before starting; do not ask again for steps already covered. Resolve material budget or task changes before proceeding.

**Manual:** use `run_start` with `method="manual"`, then `run_answers` for the requested task. It makes no model calls and requires no provider key. Present rows individually or in manageable, clearly numbered batches, keeping row IDs attached to answers. The saved task supplies the prompt and answer choices. When collecting independent judgments, keep model answers out of the presentation unless the user asks to see them.

Submit labels supplied by the user or explicitly authorized for the agent to produce. “Manual” describes how values enter rowlab, not who produced them; agent-written labels are not human ground truth. Keep their origin visible in the output name (for example, `sentiment_agent`) and delivery notes, preserving any user-required naming convention with an explicit origin note instead. Do not fill skipped rows by guessing. Null clears a label; unanswered cells stay null, with no separate abstention status.

Read saved labels using `sql_query` on the run's output dataset and progress using `run_get`. Saves replace current values; last saved value wins. An interrupted batch can be partially saved. Retry with the same values if no intervening corrections need preserving; otherwise inspect the affected rows first. There is no judgment history to recover overwritten values from. Call `run_finish` when the requested labeling is complete, reporting any unanswered labels. Finish locks the same output dataset and can safely be retried.

For an existing label file, match records by a stable key, never by row position. Join it outside rowlab before importing, or map matched records to the manual run's row IDs and submit batches using the tool's limits.

## Measure quality and choose models

Compare candidates on the same rows and task. One model run can include several models, costing one call per selected row, task and model; `columns_compare` evaluates their columns, using `reference` when there are trusted labels. A manual run can add a separate reference column to a completed model dataset. Alternatively, finish manual labeling first and run models on that output so the reference travels with the rows.

Keep a representative evaluation sample separate from targeted debugging rows. Low-confidence rows, model disagreements and rare-class enrichment are useful to inspect, but their aggregate scores do not estimate performance on the whole dataset. For population estimates, use an independent representative sample and report how it was selected, the number actually labeled, class support, and uncertainty when support is small. Avoid tuning a prompt on the same labels used to claim its final quality.

Use disagreements and comparison drilldowns to investigate model errors, prompt ambiguity and possible reference errors. Consensus does not prove the reference wrong; preserve the original reference and record any adjudicated labels separately. Without a trustworthy reference, report agreement rather than accuracy. Model self-reported confidence is not calibrated correctness.

Select metrics for the task: a rare-positive filter may need high positive recall, not high overall accuracy. Read precision and recall per relevant class. For generated text, assess a task-specific rubric on examples rather than treating exact matches as quality. Choose cost and speed among models that meet the user's quality requirements; cheaper is not automatically better.

## Run at scale

A cheap first pass can select rows for a more expensive task. Check its recall, including a sample of rejected rows: downstream processing cannot recover excluded positives. A filtered run's output contains only the selected rows, so account for exclusions in the delivered result. A confidence-based cascade needs validated thresholds; agreement with a stronger model alone is not evidence of human-grounded accuracy.

Poll `run_get` and inspect early answers when useful. Stop a costly run if its prompt is demonstrably wrong. Resume stopped or failed model runs when their configuration is still right; inspect remaining failures rather than retrying indefinitely. Identical requests can reuse cached responses, but caching is not a guarantee of exactly-once provider billing after an interrupted network call.

## Deliver

Report the output dataset and columns, answered and missing counts, actual model cost, and the quality evidence with its limits. When choosing models, also report cost per 1,000 rows on the same task and explain any cache effect that would make it unrepresentative of fresh data. Model columns carry model suffixes; manual columns use the task's output names, with option or field suffixes where the answer type requires them. Export through `dataset_export` in the requested format. When reproducibility matters, also preserve the frozen task configuration, row selection and reference-label origin outside the working dataset; do not imply that an ordinary data export includes a full provenance bundle.
