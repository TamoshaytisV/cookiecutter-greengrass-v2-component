# {{cookiecutter.component_name}}

{{cookiecutter.component_short_description}}

## Commands

### Prerequizites
You should have [poetry](https://python-poetry.org/docs/) to be installed in your system/virtualenv as a dependency management tool.

### Install dependencies

    make setup

### Create component zip file for S3 deployment

    make zip VERSION=0.1.0

By default, compressed component is located in `./dist/` folder and named `artifacts.zip`.

Compressed file contains files from specific component version folder + `requirements.txt` (production dependencies only).

### Publish artifacts to S3

    make s3-publish VERSION=0.1.0

### Publish component version to Iot Core

    make publish-version VERSION=0.1.0

### Bump component version


    make bump-version FROM=0.1.0 TO=1.0.0

`FROM` - current component version

`TO` - new component version

NOTE: By default, the command renames artifacts version folder and recipe file.

In case you want to preserve previous component version dir and recipe file, run

    make bump-version FROM=0.1.0 TO=1.0.0 KEEP_OLD_VERSION=true

### Run tests

    make tests VERSION=0.1.0

If you have several componen versions, it will be impossible for python to decide which version modules to run tests against.

For that purpose you have to provide specific `VERSION`.

After test finish, you can see coverage report using

    make coverage

### Run linters

    make lint VERSION=0.1.0

In case, if you want to add more flake8 plugins, refer to the https://github.com/DmytroLitvinov/awesome-flake8-extensions
