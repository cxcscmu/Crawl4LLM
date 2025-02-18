# Crawl4LLM

This repo contains the code for the paper "Crawl4LLM: Efficient Web Crawling for LLM Pretraining".

## Prerequisite

1. [Request the ClueWeb22 dataset](https://lemurproject.org/clueweb22/).
2. Create a virtual environment with python >= 3.10 and install the following requirements:
```
numpy
tqdm
fasttext
pyyaml
wandb
```
3. [Download the DCLM fastText classifier](https://huggingface.co/mlfoundations/fasttext-oh-eli5/tree/main) to `fasttext_scorers/`.

## Run the Crawler

To run a (simulated) crawl, first create a yaml configuration file under `configs/`, and run the following command:

```bash
python crawl.py crawl --config <path_to_your_config_file>
```

### Crawl4LLM

Create a yaml file in `configs/` with the following content:

```yaml
cw22_root_path: <path_to_clueweb22_a>
seed_docs_file: seed.txt
output_dir: crawl_results/seed_10k_crawl_20m_dclm_fasttext
num_selected_docs_per_iter: 10000
num_workers: 16  # set to a number that fits your machine
save_state_every: -1  # set to a positive number to save the state (queue & visited set) of the crawler every certain steps
max_num_docs: 20000000
selection_method: dclm_fasttext_score
order: desc  # desc for descending, asc for ascending
wandb: true  # set to false to disable wandb logging
wandb_project: crawler
wandb_run_name: seed_10k_crawl_20m_dclm_fasttext
rating_methods:
    - 
        type: length
    - 
        type: fasttext_score
        rater_name: dclm_fasttext_score
        model_path: fasttext_scorers/openhermes_reddit_eli5_vs_rw_v2_bigram_200k_train.bin
```

Documents are scored by all scorers in `rating_methods`. In the above configuration file, we set a `length` scorer, which scores a document by its length, and a `fasttext_score` scorer which uses the DCLM fastText model to score a document. The final ranking is determined by `selection_method` which is set to `dclm_fasttext_score`, the name of the `fasttext_score` scorer.

### Baseline Crawlers

#### Random Crawler

```yaml
cw22_root_path: <path_to_clueweb22_a>
seed_docs_file: seed.txt
output_dir: crawl_results/seed_10k_crawl_20m_random
num_selected_docs_per_iter: 10000
num_workers: 16
save_state_every: -1
max_num_docs: 20000000
selection_method: random_score
order: desc
wandb: true
wandb_project: crawler
wandb_run_name: seed_10k_crawl_20m_random
rating_methods:
    - 
        type: random_score
```

#### Indegree-based Crawler

```yaml
cw22_root_path: <path_to_clueweb22_a>
seed_docs_file: seed.txt
output_dir: crawl_results/seed_10k_crawl_20m_indegree
num_selected_docs_per_iter: 10000
num_workers: 16
save_state_every: -1
max_num_docs: 20000000
selection_method: inlink_count
order: desc
wandb: true
wandb_project: crawler
wandb_run_name: seed_10k_crawl_20m_indegree
rating_methods:
    - 
        type: inlink_count
```

