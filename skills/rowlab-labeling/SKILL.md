---
name: rowlab-labeling
description: Add a column to a tabular dataset that says something about every row, with rowlab. Use for classifying rows, extracting fields, summarising, translating or rewriting text, and any "label each row as…" request; works for CSV, JSON, JSONL and Parquet files, from disk, the web or S3.
---

# Labeling

Turn the request into one question per row, with an answer of a fixed type; ask it of a few rows; read the answers against the rows; then ask it of all rows. The result is a new dataset with the answer columns added, never a changed one.

## Define the question

State what one row is, and what the column must say about it. Pick the narrowest answer type that holds the answer: yes/no, pick one of listed options, pick any of them, a number on a scale, typed fields to extract, or free text (summaries, translations, rewrites). A question that needs two answers is two tasks, run together on the same rows. Options must cover every row: add one such as "not applicable" or "can't tell" when the question doesn't apply to some rows, so the model never has to guess.

Read the dataset first (`dataset_get`, with a profile for unfamiliar data) and write the prompt from the row's own columns, by name. Say what the model is looking at ("a customer review of a kitchen product") and what each option means when the words alone could be read two ways. Put a long or variable text last.

## Try on a few rows

Preview the run (`run_preview`): check each rendered prompt reads as intended, and that there are no warnings. Then run on about 20 rows (`LIMIT 20`), and read the answers next to the rows they came from, not the counts alone. Look for rows the prompt didn't anticipate: empty cells, another language, a row that is really two, text that contradicts itself. Fix the prompt or the options, and run again on the source dataset: a run's tasks are frozen, so a change is a new run, and only an identical prompt, model and row is answered from the cache for free. Twenty rows cost little either way. Ask the user only about choices that change what the column means.

When the answer is a judgement rather than a fact, or the user will act on it row by row, run a second model on the same rows and compare (`columns_compare`); disagreements are where the prompt is unclear.

## Run on all rows

Preview again for the full query, on the source dataset (the sample run's dataset already has the answer column, so the same task collides with it), and tell the user the rows, calls and estimated cost before starting; agree a budget when it is more than pocket change. Then start the run and poll it (`run_get`) without asking per step. If the first answers of the full run look wrong, stop it and start a new run with the fixed prompt; resume is for runs that stopped or failed with the prompt right, and for a done run's failed answers, which it asks again. What was answered stays answered; the rest is paid for. For large datasets or many questions, the `rowlab-labeling-at-scale` skill applies.

## Check the result

Query the finished dataset: the share of each answer, the rows with no answer, and a handful of rows per option read against their text. Compare the distribution with what the user expects of the data: all one value, or nearly uniform, is a reason to read more examples and check the options against them, not a verdict; a rare event can be rare. A `_reason` column, when the task asked for one, explains odd answers.

## Deliver

Give the user the new dataset's name and the answer columns (named `<column>__<model>`), the number of rows answered and failed, the cost, and anything the question couldn't settle. Export (`dataset_export`) where they want the data, as the format they use. For scripts and CI, rowlab's API does the same work; its docs list the endpoints.
