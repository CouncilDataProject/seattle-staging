.PHONY: help login init set-cors destroy gen-key
.DEFAULT_GOAL := help

define PRINT_HELP_PYSCRIPT
import re, sys

for line in sys.stdin:
	match = re.match(r'^([a-zA-Z_-]+):.*?## (.*)$$', line)
	if match:
		target, help = match.groups()
		print("%-20s %s" % (target, help))
endef
export PRINT_HELP_PYSCRIPT

help:
	@python -c "$$PRINT_HELP_PYSCRIPT" < $(MAKEFILE_LIST)

login: ## Run GCloud and Pulumi logins
	pulumi logout
	pulumi login
	gcloud auth login
	gcloud auth application-default login

init: ## Create google project and pulumi stack
	gcloud projects create cdp-seattle-staging-dbengvtn --set-as-default
	pulumi stack init cdp-seattle-staging-dbengvtn

set-cors: ## Set cors for the bucket
	gsutil cors set cors.json gs://cdp-seattle-staging-dbengvtn.appspot.com/

destroy: ## Fully teardown Pulumi and GCloud
	pulumi stack rm cdp-seattle-staging-dbengvtn --force
	gcloud projects delete cdp-seattle-staging-dbengvtn
	rm -f .keys/cdp-seattle-staging-dbengvtn.json

gen-key: ## Generate a service account JSON
	mkdir .keys/ -p
	gcloud iam service-accounts create cdp-seattle-staging-dbengvtn \
    	--description="CDP Dev Service Account for $(USER)" \
    	--display-name="cdp-seattle-staging-dbengvtn"
	gcloud projects add-iam-policy-binding cdp-seattle-staging-dbengvtn \
    	--member="serviceAccount:cdp-seattle-staging-dbengvtn@cdp-seattle-staging-dbengvtn.iam.gserviceaccount.com" \
		--role="roles/owner"
	gcloud iam service-accounts keys create .keys/cdp-seattle-staging-dbengvtn.json \
  		--iam-account "cdp-seattle-staging-dbengvtn@cdp-seattle-staging-dbengvtn.iam.gserviceaccount.com"