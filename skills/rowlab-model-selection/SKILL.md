---
name: rowlab-model-selection
description: Choose which model to label a dataset with, or measure how good a labelled column is, with rowlab. Use when asked which model to use, whether a cheaper or faster one will do, how accurate the answers are, or before a run on many thousands of rows where cost or quality matters.
---

# Model selection

The question is never "which model is best" but "which model is good enough for this question, at what cost". Answer it with the same rows asked of two or three models, compared.

## Set up the comparison

Take a sample of 50 to 200 rows that includes the hard cases, not the first rows of the file: `ORDER BY random()` with a `LIMIT`, or a filter for the rows that matter. Run the same task on it with each candidate model; one run with several models does this, and makes one dataset with a column per model. Candidates: the default model for the task type, a cheaper one, and a decision model when the task is classify (they are fast and cheap, and often agree with an LLM on clear-cut questions).

When the user has labels they trust (earlier annotations, a human-checked column, an outcome that was observed), run on the dataset that has them as a column: they are the reference. Labels kept elsewhere are joined to the data outside rowlab first, and the joined file added as a new dataset.

## Read the comparison

Compare the columns (`columns_compare`), with the reference column as `reference` when there is one. Agreement rates and accuracies are the summary; the disagreements are the finding. Every count comes with a `where` clause: query those rows and read them. Disagreements split into rows where the prompt is ambiguous (fix the prompt, and the models will agree), rows where one model is wrong (count which), and rows where the reference is wrong (tell the user; references are often older than the data).

Without a reference, agreement between models is not accuracy. Two models can agree on a wrong answer. When the stakes warrant it, ask the user to label 30 to 50 of the sampled rows by hand, or label them yourself from the row text when the question is factual, and use that as the reference.

## Choose

Judge quality by what the column is for, not accuracy alone. For a classifier that selects rows for a next step, the cost of a miss is what matters: on a question that is true for 2% of rows, 98% accuracy can mean every positive missed, so read precision and recall on the positives, from the reference. For summaries, translations and other written answers, two good answers differ in wording, so compare them with a short rubric (correct, complete, nothing added) on a sample read by hand, not by agreement. A confidence column (`_proba`) is only a threshold once checked against the reference; models' own confidence is often miscalibrated.

Then choose by cost and speed among the models that meet that bar, on the rows that matter. A model that is slightly less accurate at a tenth of the price is usually right for a first pass, with the expensive one taking the rows it is unsure about or where the cheap models disagree. Prefer the decision model when it matches the LLM on the sample. Don't choose on a sample that lacks the hard cases.

## Deliver

Report the sample size and how it was drawn, each model's agreement or accuracy (precision and recall where it matters), cost per thousand rows, the kinds of rows where they disagree with two or three examples, and the choice with its reason. Name the comparison dataset so the user can look at the rows themselves. For scripts, rowlab's API does the same work; its docs list the endpoints.
