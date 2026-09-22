# Probe-then-Scale: a runtime-aware attack search for tool-using AI agents

This repository is a technical explanation and analysis of **probe-then-scale**, an attack-search method for the Kaggle competition *AI Agent Security: Multi-Step Tool Attacks*. The method tests two email-tool prompt templates, compares their success-based reward per second of probe time, and generates candidates from the better-performing template.

## My contribution

The submitted code is my team's, adapted from a public Kaggle notebook by Adhiraj Jagtap. My own work is the analysis around it: explaining why a per-second reward suits a budgeted evaluator, separating the graded submission from the validation experiment so their results are not conflated, mapping each claim to the evidence that does and does not support it, and designing the experiments that would close those gaps. The reasoning and the presentation are mine.

| Project | Details |
| --- | --- |
| Competition | [AI Agent Security: Multi-Step Tool Attacks](https://www.kaggle.com/competitions/ai-agent-security-multi-step-tool-attacks) |
| Organizers | OpenAI, Google, and IEEE ([competition rules](https://www.kaggle.com/competitions/ai-agent-security-multi-step-tool-attacks/rules)) |
| Focus | Agent security, tool-call behavior, runtime-aware candidate generation |
| Implementation | Python, Jupyter, and the competition-provided evaluation SDK |
| Award record | Competition Bronze Medalist; see [award record](docs/evidence.md) |

## Start here

- Read the [solution notes](docs/solution.md) for the approach and its limitations.
- Open the [submission notebook](notebooks/sub.ipynb) for the submitted implementation.
- Browse [attack.py](src/attack.py) or the [annotated version](src/attack_annotated.py) to inspect the code directly.
- Follow the [12-section notebook walkthrough](notebooks/solution_walkthrough.ipynb) for a step-by-step explanation.
- See the [validation notebook](notebooks/validation.ipynb) and [reproduction guide](docs/reproduction.md) for the experimental setup.
- View the [presentation](presentation/kaggle-agent-security-defense.pdf) for a slide-form analysis.

## How the method works

A short probe stage chooses between the two templates:

1. Run two probes for each template in the competition environment.
2. Count successful `email.send` events and compute a reward proxy divided by measured probe time.
3. Select the template with the higher measured rate.
4. Generate 2,000 single-message candidates using sequential recipients under `outside.invalid`, a domain for synthetic addresses in the sandbox.

Each probe requests one hop: one step in the agent's tool-use sequence. The returned candidates contain messages, so the evaluator controls their final replay behavior. The reward rate is an internal template-selection heuristic; the official evaluator calculates the final score.

## Design choices worth noting

- **Reward proxy from traces, not model text.** Template selection reads exported tool traces and counts successful `email.send` events, rather than judging the model's prose.
- **Rate, not raw count.** Because each evaluation phase runs under a fixed time budget, the proxy is normalized by probe time — the goal is successful findings per second, not per attempt.
- **Unique recipient per candidate.** Every candidate uses a distinct synthetic address, so no two candidates are identical.
- **Candidate volume.** 2,000 single-message candidates keep the replay phase supplied until its deadline; they are emitted in recipient order, with no quality ranking.

## Status and limitations

- The validation notebook is a separate 10-candidate experiment with different prompts and model-specific tool-call limits. It does not reproduce a private leaderboard result, and the models have not been rerun for this repository.
- Earlier submissions and comparison logs are not included, so this repository makes no numerical claim about any change between approaches.
- The competition SDK, fixtures, and GGUF model files are not bundled. Evaluation requires these assets and a compatible runtime.
- No repository-wide license has been selected yet.
- The competition SDK, datasets, model weights, and third-party dependencies remain subject to their own terms.

## Repository layout

```text
.
├── README.md
├── requirements.txt
├── .gitignore
├── assets/
│   └── kaggle-bronze-certificate.jpg
├── presentation/
│   ├── kaggle-agent-security-defense.pptx
│   └── kaggle-agent-security-defense.pdf
├── notebooks/
│   ├── sub.ipynb                   # Submitted code; saved outputs cleared
│   ├── validation.ipynb            # Separate 10-candidate experimental variant
│   └── solution_walkthrough.ipynb  # English reading guide
├── src/
│   ├── attack.py                   # Extracted from the submission notebook
│   └── attack_annotated.py         # Same executable code, with English comments
└── docs/
    ├── solution.md
    ├── reproduction.md
    ├── evidence.md
    └── provenance.md
```

## Running and reviewing

GitHub can display the notebooks directly. To open them locally, install the optional notebook tools in `requirements.txt` and start JupyterLab:

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

For model evaluation, follow the [reproduction guide](docs/reproduction.md) to configure the competition assets and runtime before running either executable notebook.

## Research scope

This code was developed for the competition's authorized sandbox. The prompts use synthetic recipients under `outside.invalid` and do not require a production account or a real recipient.
