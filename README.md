# AWS Greengrass V2 component template

A [cookiecutter](https://github.com/cookiecutter/cookiecutter) template for [AWS Greengrass component](https://docs.aws.amazon.com/greengrass/v2/developerguide/develop-greengrass-components.html) project structure 
ready for development and deployment.

NOTE: This template is for Python>=3.7 components only. PRs to support other languages are welcome.

## Features
- Makefile with base commands to manage the component
- pytest integration
- code coverage reports
- linter checks:
  - mypy for static type checking
  - flake8 for code style checks

## Installation

### Prerequizites
Refer https://cookiecutter.readthedocs.io/en/1.7.2/installation.html for platform dependant instructions.

### Generate component
Go to the folder where you want component to be created.

Run
    
    cookiecutter https://github.com/TamoshaytisV/cookiecutter-greengrass-v2-component.git

You will be asked to provide template variable values.
