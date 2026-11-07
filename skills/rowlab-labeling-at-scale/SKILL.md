---
name: rowlab-labeling-at-scale
description: Label a large dataset with rowlab without paying for more model calls than the question needs. Use for datasets beyond tens of thousands of rows, several questions over the same rows, repeated labeling of growing data, or any run whose estimate is more than the user expects to spend.
---

# Labeling at scale

Cost is calls times price. Cut the calls before cutting the price: ask the expensive question only of the rows it applies to, ask related questions together, and never ask twice.

## Cascade

Most questions apply to a subset of the rows. Find that subset with a cheap first pass, then run the expensive task only on it. A decision model (fast, cheap, classify only) or a short yes/no prompt on a small LLM makes a good first pass: "Is this review about delivery?" over a million rows, then "What went wrong with the delivery?" over the 4% that are. The second run's SQL is a filter on the first run's column (`WHERE delivery__<model> = true`), and it makes a dataset with both columns. Check the first pass on a sample before trusting it, with the `rowlab-model-selection` skill: a first pass that misses rows loses them for good.

## Ask once

Several facts to pull from the same rows go in one extract task with several fields: one call per row answers them all. Separate tasks are each a call per row and model, so they cost as much together as apart; put them in one run for one dataset, not to save calls. The same prompt and model on the same row is never paid for twice, so iterate on a sample before the full run, and resume a stopped or failed run rather than starting a new one. When the data grows, add the new rows as a new dataset (a file of just them, with a stable id), run on it, and join the results to the earlier ones outside rowlab; a dataset is never changed in place.

## Estimate and agree

Preview the full run before starting it and tell the user the rows, calls and cost. Agree the budget and the plan (the passes, the models, the sample checks), then carry it out without asking per step, pausing only if a sample check changes what the column means. Prefer two passes that cost a tenth to one pass that is slightly more accurate, unless the user says accuracy is what matters.

## Run and watch

A run on a million rows takes hours, and its dataset's rows fill in as it goes: sample the answers early (`sql_query` with `WHERE <column> IS NOT NULL`) and stop the run if they are wrong, before the budget is spent. Some answers fail at scale (rate limits, provider errors, unusable replies); when the run is done, resume it once to ask them again, then report what is still missing. Export when the run is done.

## Deliver

Report each pass: rows in, rows selected, cost, and the sample check that justified it. Then the final dataset, its columns, failed answers, total cost against the estimate, and what was excluded by the cascade and why. For scripts and CI, rowlab's API does the same work; its docs list the endpoints.
