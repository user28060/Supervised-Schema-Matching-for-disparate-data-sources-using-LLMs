# Supervised-Schema-Matching-for-disparate-data-sources-using-LLMs

[![version](https://img.shields.io/github/v/release/okp4/template-python?style=for-the-badge&logo=github)](https://github.com/okp4/template-python/releases)
[![lint](https://img.shields.io/github/actions/workflow/status/okp4/template-python/lint.yml?label=lint&style=for-the-badge&logo=github)](https://github.com/okp4/template-python/actions/workflows/lint.yml)
[![build](https://img.shields.io/github/actions/workflow/status/okp4/template-python/build.yml?label=build&style=for-the-badge&logo=github)](https://github.com/okp4/template-python/actions/workflows/build.yml)
[![test](https://img.shields.io/github/actions/workflow/status/okp4/template-python/test.yml?label=test&style=for-the-badge&logo=github)](https://github.com/okp4/template-python/actions/workflows/test.yml)
[![codecov](https://img.shields.io/codecov/c/github/okp4/template-python?style=for-the-badge&token=G5OBC2RQKX&logo=codecov)](https://codecov.io/gh/okp4/template-python)
[![conventional commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg?style=for-the-badge&logo=conventionalcommits)](https://conventionalcommits.org)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg?style=for-the-badge)](https://github.com/semantic-release/semantic-release)
[![contributor covenant](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg?style=for-the-badge)](https://github.com/okp4/.github/blob/main/CODE_OF_CONDUCT.md)
[![License](https://img.shields.io/badge/License-BSD_3--Clause-blue.svg?style=for-the-badge)](https://opensource.org/licenses/BSD-3-Clause)

## Purpose & Philosophy

This repository holds the code and results for the paper "Supervised-Schema-Matching-for-disparate-data-sources-using-LLMs"

### Abstract

Many real-world data integration tasks must reconcile three types of tabular sources; e.g., those containing only attribute names (schema-only), those containing only data values (instance-only), and those containing both schema and values (complete). Aligning the incomplete sources with complete ones entails two distinct problems: aligning schema-only sources to complete sources, and aligning instance-only sources to complete sources. This paper presents a unified, supervised framework that addresses both scenarios by using large language models (LLMs) to generate the missing information, i.e., for schema-only tabular data, we generate plausible values; for instance-only tabular data, we infer the plausible attributes. These augmented data are then projected into a common meta space, from which we derive a rich collection of meta-features including classical metrics, spectral, and topological meta-features. A supervised classifier, which is trained on these meta-features, can predict whether any given pair -- from an incomplete and a complete source -- matches. On 371 tabular pairs of incomplete datasets from the Valentine benchmark, our approach outperforms existing supervised baselines in terms of F1-score across both alignment scenarios.

Keywords: Data Matching · Disparate Tabular Data · Auxiliary Information Generation · Meta-Space ·Machine Learning.

### Summary of the experimental evaluation

***Technical environment***

We deploy our expriments on 24 CPUNodes partitions (12 Dell C6420 servers,
dual Xeon Gold 6136 @ 3 GHz, 192 GB RAM, 7.5 GB/core). All computations
used the ONGO framework on CPUs only. Each dataset pair is split into 70%
training and 30% testing using stratified sampling to preserve class balance.
Performance is averaged over 10 independent runs with different random seeds.
To ensure reproducibility and isolate model behavior, all classifiers are evaluated
with their default hyperparameters provided by the respective libraries (scikitlearn v1.2.2, xgboost v1.7.5 and catboost v1.1.1), which guarantees a fair and
reproducible base comparison. For these experiments, we exclusively use BERT
embeddings. This choice is based on preliminary (unreported here) comparisons
across different pre-trained embedding models. BERT consistently achieved the
best performance

***Datasets***

In our experiments, we exploited the potential of five dataset categories, as described in the ["Valentine"](https://zenodo.org/records/5084605). These collections range from matching simple structured datasets to more complex configurations.

## Results

**RQ1 - Effectiveness**
This question evaluates how well the framework distinguishes matching from non-matching attribute pairs in two scenarios: schema-only → (schema, instance) and instance-only → (schema, instance). Results are reported as mean ± standard deviation over 10 runs.


**Key Findings:**

Tree-based models (CatBoost, XGBoost, Random Forest) achieve near-perfect F1-scores (≥ 0.99) in the schema-only scenario and remain strong (F1 ≥ 0.89) in the instance-only scenario. Linear models like SVM and Logistic Regression suffer from low precision, leading to lower F1-scores (~0.70). Low standard deviations across all models confirm the stability of the results. Overall, the framework shows robust effectiveness even with limited input.




**RQ2 - Efficiency**
This question analyzes runtime costs for embedding generation, meta-feature extraction, and model training/testing.



**Key Findings:**

Embedding takes longer in the instance-only scenario (151.94s vs. 110.38s) due to longer enriched texts. Meta-feature extraction is slightly slower in the schema-only scenario (~75 min), but differences remain modest. Most models train in under 25s and test in under 0.2s, except SVM (up to 1059s). Overall, the classification phase is efficient, and the full pipeline remains scalable across large datasets.



**RQ3 - Feature Importance**
This question investigates the contribution of Classical, Spectral, and Topological meta-features using an ablation study with XGBoost.


**Key Findings:**

Topological meta-features outperform others when used alone (F1 = 0.87). Combining meta-feature families improves performance further, especially in the instance-only scenario, with all three combined reaching 0.93 (instance-only) and 0.99 (schema-only). These results highlight the complementarity of the families and the generalization power of topological meta-features.



**RQ4 - Feature Selection**
This question explores the effect of reducing the number of meta-features using backward stepwise regression.



**Key Findings:**

Only 32–36 features were selected out of 89, cutting runtime by ~60% while preserving high F1-scores (0.99 for schema-only, 0.92 for instance-only). The selected meta-features cover all three families. Schema-only scenarios favor local meta-features, while instance-only favors global ones. Spectral feature selection varies by scenario, suggesting different embedding behaviors. Dimensionality reduction maintains performance while improving efficiency.



**RQ5 - Baseline**
This question compares the full and reduced Meta-Space models with SMAT and Ditto.


**Key Findings:**
The reduced model matches the full model’s performance (F1 = 0.99 and 0.92) with ~60% less runtime. Ditto performs well (F1 = 0.93 in instance-only) but does not outperform the framework. SMAT lags behind with F1-scores of 0.77 and 0.70. Overall, the reduced Meta-Space offers a competitive and scalable alternative to recent baselines.


## Conclusion

This repository tackles the problem of aligning incomplete data sources—where one side provides only schema information and the other only instance data. It proposes a supervised matching framework that leverages large language models (LLMs) to generate auxiliary information and extract meta-features from enriched representations.

The framework is evaluated on 371 dataset pairs from the Valentine Benchmark across five research questions: effectiveness, efficiency, feature importance, feature selection, and comparison with baselines. Tree-based classifiers (CatBoost, XGBoost, Random Forest) achieve near-perfect F1-scores in schema-rich settings and remain highly effective (F1 > 0.90) with instance-only data. Feature selection reduces the meta-space by over 60% with negligible performance loss.

Topological meta-features prove to be the most effective individually and contribute significantly when combined with classical and spectral meta-features. The reduced model offers competitive accuracy with improved runtime, providing a scalable and interpretable alternative to deep learning baselines like Ditto and SMAT.

All, results, and visualizations are organized by research question in the 'Experiments.ipynb' notebook.

This way, the template promotes:

- the use of [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/), [semantic versionning](https://semver.org/) and [semantic releasing](https://github.com/cycjimmy/semantic-release-action) which automates the whole package release workflow including: determining the next version number, generating the release notes, and publishing the artifacts (project tarball, docker images, etc.)
- a uniform way for managing the project lifecycle (dependencies management, building, testing)
- KISS principles: simple for developers
- a consistent coding style

## System requirements

### Python

The repository targets python `3.10` and higher.

### GPT-3.5

- **API Key**: Obtain from [OpenAI](https://platform.openai.com/docs/api-reference/introduction).

______________________________________________________________________

### 2. Example of Usage

#### GPT-3.5 Example

```python
import openai
openai.api_key = "your-api-key"
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "What is the capital of France?"}]
)
print(response["choices"][0]["message"]["content"])
# Paris
```


______________________________________________________________________

## 4. Resources

- [OpenAI API Docs](https://platform.openai.com/docs/api-reference/introduction)

### Poetry

The repository uses [Poetry](https://python-poetry.org) as python packaging and dependency management. Be sure to have it properly installed before.

```sh
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
```
Or, 
```sh
curl -sSL https://install.python-poetry.org | python3 -
```

### Docker

You can follow the link below on how to install and configure **Docker** on your local machine:

- [Docker Install Documentation](https://docs.docker.com/install/)

## What's included

This template provides the following:

- [poetry](https://python-poetry.org) for dependency management.
- [flake8](https://flake8.pycqa.org) for linting python code.
- [mypy](http://mypy-lang.org/) for static type checks.
- [pytest](https://docs.pytest.org) for unit testing.
- [click](https://palletsprojects.com/p/click/) to easily setup your project commands

The project is also configured to enforce code quality by declaring some CI workflows:

- conventional commits
- lint
- unit test
- semantic release

## Everyday activity

### Build

Project is built by [poetry](https://python-poetry.org).

```sh
poetry install
```

### Usage

```sh
poetry run schema-instance matching --help
```

Will give something like
### ---- The code will be made available afterward in the repository------
```console
Usage: schema-instance matching [OPTIONS]

  Generic matching function.

Options:
  -s1, --input-S1 TEXT            path to text file  [required]
  -i2, --input-I2 TEXT            path to text file  [required]
  --generative-model [gpt|llama]  Choose the generative model to use: 'gpt' or
                                  'llama'.
  --embedding-model [...]         Choose an embedding model from the predefined
                                  list.
  -o, --output TEXT               output directory where json file will be
                                  written  [default: .]
  -f, --force                     overwrite existing file
  --dry-run                       passthrough, will not write anything
  --help                          Show this message and exit.
```

Example:

```sh
poetry run schema-instance matching -s1 tests/inputs/datasets_instances/Magellan/Unionable/amazon_google_exp/amazon_google_exp_source.csv -i2 tests/inputs/datasets_instances/Magellan/Unionable/amazon_google_exp/amazon_google_exp_target.csv -o ./tests/outputs/results_dataframes -GenMod gpt -f
```
The pair matches outputs of the example should be in the file "tests/outputs/bert-base-uncased/matches/amazon_google_exp.json"



### Lint

> ⚠️ Be sure to write code compliant with linters or else you'll be rejected by the CI.

**Code linting** is performed by [flake8](https://flake8.pycqa.org).

```sh
poetry run flake8 --count --show-source --statistics
```

**Static type check** is performed by [mypy](http://mypy-lang.org/).

```sh
poetry run mypy .
```

To improve code quality, we use other linters in our workflows, if you don't want to be rejected by the CI,
please check these additional linters.

**Markdown linting** is performed by [markdownlint-cli](https://github.com/igorshubovych/markdownlint-cli).

```sh
markdownlint "**/*.md"
```

**Docker linting** is performed [hadolint](https://github.com/hadolint/hadolint).

```sh
hadolint Dockerfile
```

### Unit Test

> ⚠️ Be sure to write tests that succeed or else you'll be rejected by the CI.

Unit tests are performed by the [pytest](https://docs.pytest.org) testing framework.

```sh
poetry run pytest -v
```

### Build & run docker image (locally)

Build a local docker image using the following command line:

```sh
docker build -t schema-instance.
```

Once built, you can run the container locally with the following command line:

```sh
docker run -ti --rm schema-instance
```
