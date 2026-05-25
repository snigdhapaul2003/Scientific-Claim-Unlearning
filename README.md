# Scientific Claim Unlearning

Language models (LMs) are pre-trained on static scientific corpora, while scientific knowledge is dynamic and self-correcting. Scientific claims internalized by these models, can be retracted, falsified or revised later by the downstream studies, risking the propagation of incorrect information in scientific research. This motivates the need for LMs to selectively suppress obsolete scientific claims. Machine unlearning can be a natural choice as a solution, preserving overall model utility. Prior work focuses on instance-level forgetting, but scientific claims pose unique challenges due to their structured, multi-faceted, and evolving nature. We introduce a new task of Scientific Claim Unlearning and construct a benchmark SciUnlearn. To evaluate the task, we further demonstrate that existing unlearning methods fail to remove claim-level knowledge, exhibiting surface-level suppression. These findings highlight that scientific claim unlearning is fundamentally more challenging than standard fact unlearning, motivating the need for methods tailored to structured knowledge removal.

## Benchmark Dataset

General paper dataset - `Experiments/data`
Retracted paper dataset - `Experiments/data_retracted`

## Pipeline

Pipeline for building scientific question-answer datasets and running unlearning experiments.

This repository contains the code used to construct and filter two related dataset families:

- Forget set: claim-level questions extracted from source papers.
- Retain set: questions from reference papers that should remain available.

The code also includes export utilities, synchronization steps, evaluation helpers, and experiment scripts for multiple unlearning methods.

## Requirements

- Python 3.10+ recommended.
- Install dependencies with:

```bash
pip install -r requirements.txt
```

## Environment Variables

The main pipelines expect these variables to be set:

- `AZURE_API_KEY`
- `AZURE_API_BASE`
- `AZURE_API_VERSION`

Optional configuration used by some helpers:

- `S2_API_KEY`

Create a `.env` file or set them in your shell before running the scripts.

## Repository Layout

- `SciUnlearn/` - main Python package with the data pipeline and utilities.
- `SciUnlearn/config.py` - Set the hyparameters. 
- `SciUnlearn/forget_main.py` - builds the forget pipeline.
- `SciUnlearn/retain_external_main.py` - builds the external retain pipeline.
- `SciUnlearn/retain_internal_main.py` - builds the internal retain pipeline.
- `SciUnlearn/keep_common_across_all.py` - Keeps only the common papers after filtering.
- `SciUnlearn/export_main.py` - builds the forget set export.

You need to run the above files one by one to create the SciUnlearn Dataset. 

Run these scripts from the repository root:

```bash
python SciUnlearn/forget_main.py
python SciUnlearn/retain_external_main.py
python SciUnlearn/retain_internal_main.py
python SciUnlearn/keep_common_across_all.py
python SciUnlearn/export_main.py
```

The json and parquet files of the generated data with be stored in the folder `SciUnlearn/common_exports/`


- `Experiments/` - unlearning and evaluation experiment scripts.
- `Experiments/data/` - example exported datasets in JSONL and Parquet format.

To run the experiments follow the command as follows:

- For GD with LORA - 
```bash
python Experiments/unlearn_gd_7B.py
```
- For GD full parameter unlearning - 
```bash
python Experiments/unlearn_gd_full_7B.py
```
- For NPO with LORA - 
```bash
python Experiments/unlearn_npo_7B.py
```
- For NPO full parameter unlearning - 
```bash
python Experiments/unlearn_npo_full_7B.py
```
-For NPO+RT with LORA
```bash
python Experiments/unlearn_npo_rt_7B.py
```
-For NPO+RT full parameter unlearning
```bash
python Experiments/unlearn_npo_rt_full_7B.py
```
-For SimNPO with LORA
```bash
python Experiments/unlearn_simnpo_7B.py
```
-For SimNPO full parameter unlearning
```bash
python Experiments/unlearn_simnpo_full_7B.py
```
-For SimNPO+RT with LORA
```bash
python Experiments/unlearn_simnpo_rt_7B.py
```
-For SimNPO+RT full parameter unlearning
```bash
python Experiments/unlearn_simnpo_rt_full_7B.py
```
-For evaluation run 
```bash
python evaluate_unlearned_model_lora.py --model_path saved_model_folder_path --forget_path_1 data/forget_sc_1.jsonl --forget_path_2 data/forget_sc_2.jsonl --retain_external data/retain_external_sc.jsonl --retain_internal data/retain_internal_sc.jsonl --output_dir eval_results --benchmarks mmlu arc_challenge hellaswag 
```

The evaluated result on all the input files will be saved to `Experiments/eval_results`

## Notes

- The repository contains generated data artifacts as well as source code.
- Some scripts are designed to be run only after earlier stages have produced their inputs.
- Large model validation can be expensive, so make sure the required API and model access is available before running full runs.
