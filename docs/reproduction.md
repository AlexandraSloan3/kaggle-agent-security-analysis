# Reproduction guide

## Scope

These are the competition notebooks, with their code preserved and saved execution outputs cleared for publication. The repository checks cover source structure and code consistency. The models and competition evaluation have not been rerun for this public release, so it does not include a fresh reproduction of a private leaderboard score.

## Read the project

GitHub renders the notebooks without installing anything. The optional `requirements.txt` installs JupyterLab for local viewing. It does not install the competition SDK, model weights, CUDA, or the inference stack.

The executable notebooks record Python 3.12.13 in their metadata. This repository does not include a complete dependency lockfile or the exact inference-stack version, so that metadata alone is not enough to recreate the original environment.

## Required evaluation assets

Obtain the competition data and models through the [official competition page](https://www.kaggle.com/competitions/ai-agent-security-multi-step-tool-attacks), under their applicable access conditions and terms.

The validation notebook expects these paths:

```text
/kaggle/input/competitions/ai-agent-security-multi-step-tool-attacks
/kaggle/input/models/llkh0a/gpt-oss-20b-gguf/pytorch/default/1/gpt_oss/gpt-oss-20b-Q4_K_M.gguf
/kaggle/input/models/llkh0a/gemma-4-26b-a4b-it-ud-q4-k-m-gguf/pytorch/default/1/gemma/gemma-4-26B-A4B-it-UD-Q4_K_M.gguf
```

These paths come from the original notebook. They have not been checked as current model-download links. Adjust the three path constants if your mounted dataset or model versions differ. Model files and SDK code are excluded from this repository.

The validation notebook contains a conditional installer for `llama-cpp-python`, using a CUDA 12.4 wheel index when `llama_cpp` is absent. The installer does not pin a package version. Check compatibility with the actual runtime; a successful installation alone does not establish equivalence with the original evaluation environment.

## Submission notebook

1. Open `notebooks/sub.ipynb` in a compatible competition environment with the required assets attached.
2. Use a clean working directory. The first cell writes `attack.py` into the current directory.
3. Run the first cell, then the server/bootstrap cell using the competition's execution workflow.
4. Inspect the execution log and evaluator-produced result. The placeholder CSV is not evaluation evidence.

The bootstrap uses `/kaggle/working` when it exists; otherwise it writes the placeholder under `submissions/local_working`. The original code is preserved, including its working-directory assumptions.

## Validation notebook

1. Open `notebooks/validation.ipynb` in the corresponding Kaggle/Linux environment.
2. Ensure the notebook's current working directory is `/kaggle/working`. Its `%%writefile attack.py` cell must write the same file that `ATTACK_PATH` later loads.
3. Check the competition and model paths in the setup cell.
4. Run the cells in order. The final model loop evaluates GPT-OSS and Gemma sequentially.
5. Inspect `/kaggle/working/artifacts` for summaries and diagnostic logs.

The notebook uses Linux paths and a `df` command. It is not ready for unchanged execution in a Windows Jupyter session.

### Compare the submitted algorithm

The validation notebook writes an experimental 10-candidate algorithm. To evaluate the submitted version in a separate experiment, replace that write-file cell's Python body with the contents of `src/attack.py`, retaining the `%%writefile attack.py` first line. Check the model hop-limit policy separately and record the change alongside the result. The included validation notebook keeps its original experimental configuration.

Neither implementation reads the candidate count from `ATTACK_CONFIG`. Change `MAX_CANDIDATES` explicitly when designing a smaller smoke test and record that change.

## Interpret results

- The validation harness uses the public guardrail and custom hop policies.
- The attack's template-selection proxy and the harness's computed scores are different quantities.
- Successful email-tool events do not independently establish an official security predicate or score.
- Timing depends on the evaluation runtime. Fixed seeds do not guarantee matching timing or identical outputs across different inference stacks.
- Historical execution outputs are cleared in the publication copies. This release contains no freshly reproduced model results.

For a reproducibility record, retain the SDK version, model identifiers and hashes, inference-stack versions, hardware, relevant configuration, notebook revision, and generated reports.
