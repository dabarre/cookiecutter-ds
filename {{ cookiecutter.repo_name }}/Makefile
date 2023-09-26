.PHONY: clean data lint format {% if cookiecutter.dependency_file != 'none' %}requirements{% endif %} {% if cookiecutter.dependency_file != 'none' %}sync_data_down sync_data_up{% endif %}

#################################################################################
# GLOBALS                                                                       #
#################################################################################

PROJECT_DIR := $(shell dirname $(realpath $(lastword $(MAKEFILE_LIST))))
PROJECT_NAME = {{ cookiecutter.repo_name }}
PYTHON_VERSION = {{ cookiecutter.python_version_number }}
PYTHON_INTERPRETER = {{ cookiecutter.python_interpreter }}
MLFLOW_DIR = to_set
{% if cookiecutter.environment_manager != 'none' %}PROJECT_VENV_NAME = venv{% endif %}


#################################################################################
# COMMANDS                                                                      #
#################################################################################

{% if cookiecutter.dependency_file != 'none' %}
## Install python dependencies for production
requirements:
	{% if "requirements.txt" == cookiecutter.dependency_file -%}
	$(PYTHON_INTERPRETER) -m pip install --upgrade pip
	$(PYTHON_INTERPRETER) -m pip install -r requirements.txt
	{% elif "environment.yml" == cookiecutter.dependency_file -%}
	conda env update --name $(PROJECT_NAME) --file environment.yml --prune
	{% elif "Pipfile" == cookiecutter.dependency_file -%}
	pipenv install
	{% endif %}
{% endif %}

## Install python dependencies for development
requirements_dev: requirements
	$(PYTHON_INTERPRETER) -m pip install --upgrade -r requirements_dev.txt


save_requirements:
	pipreqs $(PROJECT_DIR) --force

## Reinstall local module
reinstall:
	rm -rf $(MODULE_NAME).egg-info
	pip install -e .

## Delete all compiled Python files
clean:
	find . -type f -name "*.py[co]" -delete
	find . -path "*/__pycache__/*" -delete
	find . -type d -name "__pycache__" -delete
	rm -rf .pytest_cache


## Launch api
api:
	@echo Write command to launch api

## Launch MLFlow server
mlflow_server:
# mlflow server --host 0.0.0.0 --port 5000 --backend-store-uri $(MLFLOW_DIR) --artifacts-destination $(MLFLOW_DIR)
	@echo Write command to launch mlflow server

## Test code with pytest
check:
	pytest test

## Lint code and format it
lint: format lint

## Format source code and sort imports
format: isort black

# Lint code with flake8
flake8:
	flake8 {{ cookiecutter.module_name }}
	falke8 test

# Format code with black
black:
	black --config pyproject.toml {{ cookiecutter.module_name }}
	black --config pyproject.toml test

# Sort imports with isort
isort:
	isort --settings-path pyproject.toml {{ cookiecutter.module_name }}
	isort --settings-path pyproject.toml test



{% if not cookiecutter.dataset_storage.none %}
## Download Data from storage system
sync_data_down:
	{% if cookiecutter.dataset_storage.s3 -%}
	aws s3 sync s3://{{ cookiecutter.dataset_storage.s3.bucket }}/data/\
		data/ {% if cookiecutter.dataset_storage.s3.aws_profile != 'default' %} --profile {{ cookiecutter.dataset_storage.s3.aws_profile }}{% endif %}
	{% elif cookiecutter.dataset_storage.azure -%}
	az storage blob download-batch -s {{ cookiecutter.dataset_storage.azure.container }}/data/ \
		-d data/
	{% elif cookiecutter.dataset_storage.gcs -%}
	gsutil -m rsync -r gs://{{ cookiecutter.dataset_storage.gcs.bucket }}/data/ data/
	{% endif %}

## Upload Data to storage system
sync_data_up:
	{% if cookiecutter.dataset_storage.s3 -%}
	aws s3 sync s3://{{ cookiecutter.dataset_storage.s3.bucket }}/data/ data/\
		{% if cookiecutter.dataset_storage.s3.aws_profile %} --profile $(PROFILE){% endif %}
	{% elif cookiecutter.dataset_storage.azure -%}
	az storage blob upload-batch -d {{ cookiecutter.dataset_storage.azure.container }}/data/ \
		-s data/
	{% elif cookiecutter.dataset_storage.gcs -%}
	gsutil -m rsync -r data/ gs://{{ cookiecutter.dataset_storage.gcs.bucket }}/data/
	{% endif %}
{% endif %}

{% if cookiecutter.environment_manager != 'none' %}
## Set up python interpreter environment
create_environment:
	{% if cookiecutter.environment_manager == 'conda' -%}
	{% if cookiecutter.dependency_file != 'environment.yml' %}
	conda create --name $(PROJECT_NAME) python=$(PYTHON_VERSION) -y
	{% else -%}
	conda env create --name $(PROJECT_NAME) python=$(PYTHON_VERSION) -f environment.yml
	{% endif %}
	@echo ">>> conda env created. Activate with:\nconda activate $(PROJECT_NAME)"
    {% elif cookiecutter.environment_manager == 'virtualenv' -%}
	virtualenv $(PROJECT_VENV_NAME) --python=$(PYTHON_INTERPRETER)
	@echo ">>> New virtualenv created. Activate with:\nsource venv/Scripts/activate"
	{% elif cookiecutter.environment_manager == 'virtualenvwrapper' -%}
	@bash -c "if [ ! -z `which virtualenvwrapper.sh` ]; then source `which virtualenvwrapper.sh`; mkvirtualenv $(PROJECT_NAME) --python=$(PYTHON_INTERPRETER); else mkvirtualenv.bat $(PROJECT_NAME) --python=$(PYTHON_INTERPRETER); fi"
	@echo ">>> New virtualenv created. Activate with:\nworkon $(PROJECT_NAME)"
	{% elif cookiecutter.environment_manager == 'pipenv' -%}
	pipenv --python $(PYTHON_VERSION)
	@echo ">>> New pipenv created. Activate with:\npipenv shell"
	{% endif %}
{% endif %}


#################################################################################
# PROJECT RULES                                                                 #
#################################################################################

## Make Dataset
data: requirements
	$(PYTHON_INTERPRETER) {{ cookiecutter.module_name }}/data/make_dataset.py

#################################################################################
# Self Documenting Commands                                                     #
#################################################################################

.DEFAULT_GOAL := help

# Inspired by <http://marmelab.com/blog/2016/02/29/auto-documented-makefile.html>
# sed script explained:
# /^##/:
# 	* save line in hold space
# 	* purge line
# 	* Loop:
# 		* append newline + line to hold space
# 		* go to next line
# 		* if line starts with doc comment, strip comment character off and loop
# 	* remove target prerequisites
# 	* append hold space (+ newline) to line
# 	* replace newline plus comments by `---`
# 	* print line
# Separate expressions are necessary because labels cannot be delimited by
# semicolon; see <http://stackoverflow.com/a/11799865/1968>
.PHONY: help
help:
	@echo "$$(tput bold)Available rules:$$(tput sgr0)"
	@echo
	@sed -n -e "/^## / { \
		h; \
		s/.*//; \
		:doc" \
		-e "H; \
		n; \
		s/^## //; \
		t doc" \
		-e "s/:.*//; \
		G; \
		s/\\n## /---/; \
		s/\\n/ /g; \
		p; \
	}" ${MAKEFILE_LIST} \
	| LC_ALL='C' sort --ignore-case \
	| awk -F '---' \
		-v ncol=$$(tput cols) \
		-v indent=19 \
		-v col_on="$$(tput setaf 6)" \
		-v col_off="$$(tput sgr0)" \
	'{ \
		printf "%s%*s%s ", col_on, -indent, $$1, col_off; \
		n = split($$2, words, " "); \
		line_length = ncol - indent; \
		for (i = 1; i <= n; i++) { \
			line_length -= length(words[i]) + 1; \
			if (line_length <= 0) { \
				line_length = ncol - indent - length(words[i]) - 1; \
				printf "\n%*s ", -indent, " "; \
			} \
			printf "%s ", words[i]; \
		} \
		printf "\n"; \
	}' \
	| more $(shell test $(shell uname) = Darwin && echo '--no-init --raw-control-chars')
