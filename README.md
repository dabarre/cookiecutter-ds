# Cookiecutter for Data Science

_A logical, reasonably standardized, but flexible project structure for doing and sharing data science work._
- It is based on the [Cookiecutter Data Science](https://github.com/drivendata/cookiecutter-data-science) template.
- Cookiecutter Data Science fundamental guidelines: [here](https://drivendata.github.io/cookiecutter-data-science/)

### Features
- Enviroment Manager (virtualenv is currently my preferred tool)
- MLFlow
- FastAPI
- Consistent code quality: isort, black and flake8
- Pytest

### Quickstart

Requirements:
- Python 2.7 or 3.5+
- [Cookiecutter Python package](http://cookiecutter.readthedocs.org/en/latest/installation.html)

1. Install cookiecutter
``` bash
pip install cookiecutter
```

or

``` bash
conda config --add channels conda-forge
conda install cookiecutter
```

2. Create a new project:
``` bash
cookiecutter https://github.com/dabarre/cookiecutter-ds/
```

Then the cookiecutter command will ask the following values about the project:
<details>
<summary>Click to expand</summary>

```
project_name
repo_name
module_name
author_name
description
python_version_number
dataset_storage
environment_manager
dependency_file
python_interpreter
open_source_license
```

</details>

3. Initialise a new Git repository:
``` bash
cd <NEW_PROJECT_DIR>
git init
git add.
git commit -m "Initial commit"
git remote add origin <GIT_REMOTE_URL>
git push -u origin master
```

4. Create environment:
``` bash
make create_environment
```

5. Start working


### Initial directories and files
The directory structure of your new project looks like this: 

```
{{ cookiecutter.repo_name }}
│
├── configs                     <- Config files (models and training hyperparameters)
│   └── model1.yaml
│
├── data                                                 
│   ├── external                <- Data from third party sources.
│   ├── interim                 <- Intermediate data that has been transformed.
│   ├── processed               <- The final, canonical data sets for modeling.
│   └── raw                     <- The original, immutable data dump.
│
├── docs                        <- Project documentation; by default a sphinx project.
│
├── models                      <- Trained and serialized models, model predictions, or model summaries.
│
├── notebooks                   <- Jupyter notebooks. Naming convention is a number (for ordering),
│                                  the creator's initials, and a short `-` delimited description
│                                  e.g. `1.0-jqp-initial-data-exploration`.
│
├── references                  <- Data dictionaries, manuals, and all other explanatory materials.
│
├── reports                     <- Generated analysis as HTML, PDF, LaTeX, etc.
│   └── figures                 <- Generated graphics and figures to be used in reporting.
│
├── tests
│   ├── fixtures                <- Where to put static test data
│   │   ├── input.json          <- Test input data
│   │   └── output.json         <- Test output data
│   └── test.py                 <- Code tests
│
├── {{ cookiecutter.module_name }}      <- Source code for use in this project.
│   │
│   ├── __init__.py    <- Makes {{ cookiecutter.module_name }} a Python module
│   │
│   ├── data           <- Scripts to download or generate data
│   │   └── make_dataset.py
│   │
│   ├── features       <- Scripts to turn raw data into features for modeling
│   │   └── build_features.py
│   │
│   ├── models         <- Scripts to train models and then use trained models to make
│   │   │                 predictions
│   │   ├── predict_model.py
│   │   └── train_model.py
│   │
│   └── visualization  <- Scripts to create exploratory and results oriented visualizations
│       └── visualize.py
│
├── .env                        <- Project's enviroment variables
├── .gitignore
├── LICENSE
├── Makefile                    <- Makefile with commands like `make data` or `make train`
├── README.md
├── requirements-dev.txt        <- The requirements file for reproducing the development environment.
├── requirements.txt            <- The requirements file for reproducing the analysis environment.
├── setup.cfg                   <- Configuration file for flake8 and pep8
├── setup.py                    <- Makes project pip installable (pip install -e .) so
│                                  {{ cookiecutter.module_name }} can be imported.
└── pyproject.toml              <- Configuration file for black
```
