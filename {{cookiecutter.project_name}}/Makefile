S3_PATH={{cookiecutter.s3_bucket_path}}
COMPONENT_NAME={{cookiecutter.component_name}}
ARTIFACTS_DIR=artifacts/$(COMPONENT_NAME)
RECIPES_DIR=recipes
DIST_DIR=$(shell pwd)/dist
ZIP_FILE_NAME=artifacts
ZIP_FILE_PATH=$(wildcard dist/*zip)
COVERAGE_PERCENT=80

.PHONY: setup zip s3-publish bump-version generate-dependencies tests coverage lint GUARD

GUARD:

guard-%: GUARD
	@ if [ -z '${${*}}' ]; then echo 'Command parameter $* not set.' && exit 1; fi
	@ if [ ! -d '$(ARTIFACTS_DIR)/$(VERSION)' ]; then echo 'The component $(VERSION) version folder does not exist' && exit 1; fi

generate-dependencies: guard-VERSION
	poetry update
	poetry export -f requirements.txt --output $(ARTIFACTS_DIR)/$(VERSION)/requirements.txt

setup:
	poetry install

zip: guard-VERSION generate-dependencies
	mkdir -p ./dist \
	&& rm -f dist/*.zip \
	&& cd $(ARTIFACTS_DIR)/${VERSION} \
	&& zip -r $(DIST_DIR)/$(ZIP_FILE_NAME).zip .

s3-publish: guard-VERSION
	aws s3 cp $(RECIPES_DIR)/$(COMPONENT_NAME)-$(VERSION).json \
		$(S3_PATH)/$(RECIPES_DIR)/$(COMPONENT_NAME)-$(VERSION).json
	aws s3 cp $(ZIP_FILE_PATH) \
		$(S3_PATH)/$(ARTIFACTS_DIR)/$(VERSION)/$(notdir $(ZIP_FILE_PATH))

publish-version: guard-VERSION
	aws greengrassv2 create-component-version --inline-recipe fileb://$(RECIPES_DIR)/$(COMPONENT_NAME)-$(VERSION).json

bump-version: guard-FROM guard-TO
	@ if [ ! -z "$(KEEP_OLD_VERSION)" ]; then \
		cp $(RECIPES_DIR)/$(COMPONENT_NAME)-$(FROM).json $(RECIPES_DIR)/$(COMPONENT_NAME)-$(TO).json; \
		cp -r $(ARTIFACTS_DIR)/$(FROM) $(ARTIFACTS_DIR)/$(TO); \
	else \
		mv $(RECIPES_DIR)/$(COMPONENT_NAME)-$(FROM).json $(RECIPES_DIR)/$(COMPONENT_NAME)-$(TO).json; \
		mv $(ARTIFACTS_DIR)/$(FROM)/* $(ARTIFACTS_DIR)/$(TO); \
		rm -rf $(ARTIFACTS_DIR)/$(FROM); \
	fi
	sed -i '' -e '/ComponentVersion/s/$(FROM)/$(TO)/;/U[Rr][Ii]/s/$(FROM)/$(TO)/g' $(RECIPES_DIR)/$(COMPONENT_NAME)-$(TO).json

tests: export PYTHONPATH=$(ARTIFACTS_DIR)/$(VERSION)
tests: guard-VERSION
	poetry run pytest --cov=$(ARTIFACTS_DIR)/$(VERSION) .

coverage:
	poetry run coverage report -m --fail-under $(COVERAGE_PERCENT)

lint: export PYTHONPATH=$(ARTIFACTS_DIR)/$(VERSION)
lint: guard-VERSION
	poetry run flakehell lint $(ARTIFACTS_DIR)/$(VERSION)
	poetry run mypy $(ARTIFACTS_DIR)/$(VERSION)
