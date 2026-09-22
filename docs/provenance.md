# About the files

This repository brings together the submission, a validation experiment, English explanations, and the Kaggle certificate. The submitted code is my team's, adapted from a public Kaggle notebook by Adhiraj Jagtap. The material here is my technical analysis of that method.

## Source and attribution

- The submitted algorithm was adapted from a public Kaggle notebook by **Adhiraj Jagtap**. Structurally similar probe-and-expand solutions appear in other public write-ups for this competition.
- The submitted code was integrated and submitted by the team; the certificate is issued per participant.
- The analysis, limitations, validation-vs-submission comparison, next-experiment design, and presentation in this repository are my own.

## Original code and documentation

| File | Where it comes from |
| --- | --- |
| `notebooks/sub.ipynb` | The submitted notebook, with code-cell sources preserved and saved outputs and execution metadata cleared |
| `src/attack.py` | The first code cell of the submission, excluding the `%%writefile` directive |
| `src/attack_annotated.py` | The same Python statements as `src/attack.py`, with English comments added for this repository |
| `notebooks/validation.ipynb` | The validation notebook, with code-cell sources preserved, outputs cleared, and Markdown notes clarified to match its paths and configuration |
| `notebooks/solution_walkthrough.ipynb` | An English adaptation of the original explanatory notebook, organized as a reading guide and checked against the submission |
| `docs/solution.md` | An explanation of the implementation and the analysis behind it |
| `presentation/` | Slide-form analysis prepared for this repository (PPTX and PDF) |
| `assets/kaggle-bronze-certificate.jpg` | The certificate image, copied without alteration; the name, rank, team count, medal, and award date are transcribed in `docs/evidence.md` |

## Changes for publication

The walkthrough is an edited English adaptation rather than a line-for-line translation. Duplicate code listings, unverified comparisons with other teams, and links to slide decks not included here are omitted. Its explanations of the reward proxy, recipient ordering, unused constants, and validation differences match the actual submission.

The publication copies omit notebook outputs, execution counts, and platform execution metadata. Language and kernel metadata are retained, and the original input files remain unchanged.

## Checks for this release

The files have been checked for Python syntax, notebook structure, unchanged executable notebook code, equivalence of the annotated source, English publication text, and working local documentation links. The published certificate matches the original image file. These file checks do not run the models or independently verify the result against Kaggle's live leaderboard. Model evaluation and certificate details are described separately in the reproduction guide and award page.
