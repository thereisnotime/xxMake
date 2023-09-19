#!make
ENV := $(PWD)/.env
include $(ENV)
export

#########################################
# TF Makefile
#########################################

# TODO: Solve case when .env does not exist.

# Config
# SHELL := /bin/bash

# Define live updates
MAKE_REPOSITORY := https://raw.githubusercontent.com/thereisnotime/xxMake/master/terraform/Makefile
MAKE_VERSION := 1.50

# Define variables
TF_MORE_ARGS := -compact-warnings
TF_AUTO_APPLY := -auto-approve
TF_PARALLELISM := 10
TF_PLAN := ""

# Define colors
COLOR_DEFAULT := \033[0m
COLOR_GREEN := \033[0;32m
COLOR_YELLOW := \033[0;33m

# Define targets
.PHONY: help
help:
	@echo "$(COLOR_GREEN)Available targets:$(COLOR_DEFAULT)"
	@echo "  $(COLOR_YELLOW)init$(COLOR_DEFAULT)        Initialize Terraform"
	@echo "  $(COLOR_YELLOW)validate$(COLOR_DEFAULT)    Validate Terraform configuration"
	@echo "  $(COLOR_YELLOW)fmt$(COLOR_DEFAULT)         Format Terraform configuration"
	@echo "  $(COLOR_YELLOW)plan$(COLOR_DEFAULT)        Generate Terraform plan"
	@echo "  $(COLOR_YELLOW)apply$(COLOR_DEFAULT)       Apply Terraform changes"
	@echo "  $(COLOR_YELLOW)destroy$(COLOR_DEFAULT)     Destroy Terraform resources"
	@echo "  $(COLOR_YELLOW)taint$(COLOR_DEFAULT)       Taint Terraform resources"
	@echo "  $(COLOR_YELLOW)taintm$(COLOR_DEFAULT)      Taint whole Terraform module"
	@echo "  $(COLOR_YELLOW)untaint$(COLOR_DEFAULT)     Untaint Terraform resources"
	@echo "  $(COLOR_YELLOW)console$(COLOR_DEFAULT)     Open Terraform console"
	@echo "  $(COLOR_YELLOW)clean$(COLOR_DEFAULT)       Clean up Terraform artifacts"
	@echo "  $(COLOR_YELLOW)graph$(COLOR_DEFAULT)       Generates graph of all dependencies from the plan"
	@echo "  $(COLOR_YELLOW)docs$(COLOR_DEFAULT)        Generates documentation (README.md) for the modules"
	@echo "  $(COLOR_YELLOW)cost$(COLOR_DEFAULT)        Calculate estimated infrastructure costs"
	@echo "  $(COLOR_YELLOW)tfsec$(COLOR_DEFAULT)       Run tfsec to check for security issues"
	@echo "  $(COLOR_YELLOW)semgrep$(COLOR_DEFAULT)     Run semgrep to check for security issues on the current folder (FAST)"
	@echo "  $(COLOR_YELLOW)kics$(COLOR_DEFAULT)        Runs KICS to perform SAST on the code"
	@echo "  $(COLOR_YELLOW)providers$(COLOR_DEFAULT)   Lists TF providers and their module dependencies (FAST)"
	@echo "  $(COLOR_YELLOW)megalint$(COLOR_DEFAULT)    Run Mega Linter on the whole repo (SLOW)"
	@echo "  $(COLOR_YELLOW)info$(COLOR_DEFAULT)        Shows providers, output, state and more"
	@echo "  $(COLOR_YELLOW)tfk8s$(COLOR_DEFAULT)       Converts K8s yaml 'input.yaml' to Terraform resource 'converted.tf'"
	@echo "  $(COLOR_YELLOW)req$(COLOR_DEFAULT)         Checks local dependency requirements"
	@echo "  $(COLOR_YELLOW)rmstate$(COLOR_DEFAULT)     Deletes the remote state. Use with caution!"
	@echo "  $(COLOR_YELLOW)upgrade$(COLOR_DEFAULT)     Performs terraform init -upgrade"
	@echo "  $(COLOR_YELLOW)update-self$(COLOR_DEFAULT) Updates Makefile to latest"

.PHONY: check-env
check-env:
	@test -f .env || (echo "ERROR: .env file not found." && exit 1)

.PHONY: update-self
update-self:
	@IS_TEMPLATE=$$(if grep -q "include " Makefile; then echo "1"; else echo "0"; fi); \
	MAIN_MAKEFILE_DIR="."; \
	if [ $$IS_TEMPLATE -eq 1 ]; then \
		MAIN_MAKEFILE_DIR=$$(grep "include " Makefile | awk '{print $$2}'); \
		echo "This is a template Makefile. Updating the main Makefile in $$MAIN_MAKEFILE_DIR."; \
	fi; \
	echo "Fetching current and new MAKE_VERSION..."; \
	curl --connect-timeout 5 -H 'Cache-Control: no-cache, no-store' --silent -o "$$MAIN_MAKEFILE_DIR.new" $(MAKE_REPOSITORY); \
	CURRENT_VERSION_DOT=$$(grep '^MAKE_VERSION := ' "$$MAIN_MAKEFILE_DIR" | awk '{print $$3}'); \
	CURRENT_VERSION=$$(echo "$$CURRENT_VERSION_DOT" | tr -d '.'); \
	NEW_VERSION_DOT=$$(grep '^MAKE_VERSION := ' "$$MAIN_MAKEFILE_DIR.new" | awk '{print $$3}'); \
	NEW_VERSION=$$(echo "$$NEW_VERSION_DOT" | tr -d '.'); \
	echo "Current MAKE_VERSION is $(COLOR_YELLOW)$$CURRENT_VERSION_DOT$(COLOR_DEFAULT)"; \
	echo "New MAKE_VERSION is $(COLOR_GREEN)$$NEW_VERSION_DOT$(COLOR_DEFAULT)"; \
	IS_NEWER=$$(awk 'BEGIN {print (ARGV[1] < ARGV[2])}' $$CURRENT_VERSION $$NEW_VERSION); \
	if [ $$IS_NEWER -eq 1 ]; then \
		echo "$(COLOR_GREEN)Updating Makefile from version $(COLOR_YELLOW)$$CURRENT_VERSION_DOT$(COLOR_GREEN) to $$NEW_VERSION_DOT$(COLOR_DEFAULT)"; \
		mv "$$MAIN_MAKEFILE_DIR.new" "$$MAIN_MAKEFILE_DIR"; \
	else \
		echo "Current version is $(COLOR_GREEN)up-to-date$(COLOR_DEFAULT). No need for update."; \
		rm "$$MAIN_MAKEFILE_DIR.new"; \
	fi

.PHONY: init
init: check-env
	@echo "$(COLOR_GREEN)Initializing Terraform...$(COLOR_DEFAULT)"
	terraform init $(TF_MORE_ARGS)

.PHONY: validate
validate: 
	@echo "$(COLOR_YELLOW)Validating Terraform configuration...$(COLOR_DEFAULT)"
	terraform validate $(TF_MORE_ARGS)

.PHONY: fmt
fmt: 
	@echo "$(COLOR_YELLOW)Formatting Terraform configuration...$(COLOR_DEFAULT)"
	terraform fmt -recursive $(PWD)

.PHONY: plan
plan: check-env init fmt
	@echo "$(COLOR_YELLOW)Generating Terraform plan...$(COLOR_DEFAULT)"
	terraform plan $(TF_MORE_ARGS)

.PHONY: unlock
unlock:
	@echo "$(COLOR_YELLOW)Unlocking Terraform state...$(COLOR_DEFAULT)"; \
    PLAN=$$( terraform plan $(TF_MORE_ARGS) 2>&1 | grep -oE 'ID:\s+[0-9a-f-]+' | awk '{print $$2}' ); \
	if [ -z "$$PLAN" ]; then \
		echo "No lock found"; \
	else \
		echo "Lock ID: $$PLAN"; \
		terraform force-unlock -force $$PLAN; \
	fi

PHONE: graph
graph:
	@echo "$(COLOR_YELLOW)Generating Terraform graph...$(COLOR_DEFAULT)"
	terraform graph | dot -Tsvg > graph.svg

PHONY: state
state: check-env
	@echo "$(COLOR_YELLOW)Showing current Terraform state...$(COLOR_DEFAULT)"
	terraform state list

.PHONY: info
info: check-env
	@echo "$(COLOR_GREEN)Showing providers...$(COLOR_DEFAULT)"
	terraform providers
	@echo "$(COLOR_GREEN)Showing workspaces...$(COLOR_DEFAULT)"
	terraform workspace list
	@echo "$(COLOR_GREEN)Showing version...$(COLOR_DEFAULT)"
	terraform --version
	@echo "$(COLOR_GREEN)Showing outputs...$(COLOR_DEFAULT)"
	terraform output

.PHONY: apply
apply: init fmt
	@echo "$(COLOR_GREEN)Applying Terraform plan...$(COLOR_DEFAULT)"
	terraform apply -parallelism=$(TF_PARALLELISM) $(TF_MORE_ARGS) ${TF_AUTO_APPLY}

.PHONY: console
console: check-env
	@echo "$(COLOR_GREEN)Opening console...$(COLOR_DEFAULT)"
	terraform console $(TF_MORE_ARGS)

.PHONY: providers
providers: check-env
	@echo "$(COLOR_GREEN)Getting providers...$(COLOR_DEFAULT)"
	terraform providers

.PHONY: taint
taint: check-env
	@echo "$(COLOR_GREEN)Tainting: $(strip $(filter-out $@,$(MAKECMDGOALS)))...$(COLOR_DEFAULT)"
	terraform taint $(strip $(filter-out $@,$(MAKECMDGOALS)))

.PHONY: taintm
taintm:
	@echo "Tainting module: $(strip $(filter-out $@,$(MAKECMDGOALS)))"
	@addresses=$$(terraform state list "module.$(strip $(filter-out $@,$(MAKECMDGOALS)))") ;\
	for addr in $$addresses ;\
	do \
		terraform taint "$$addr" ;\
	done

.PHONY: untaint
untaint: check-env
	@echo "$(COLOR_GREEN)Untainting...$(COLOR_DEFAULT)"
	terraform untaint $(strip $(filter-out $@,$(MAKECMDGOALS)))

.PHONY: destroy
destroy: 
	@echo "$(COLOR_GREEN)Destroying Terraform resources...$(COLOR_DEFAULT)"
	terraform destroy -parallelism=$(TF_PARALLELISM) $(TF_MORE_ARGS)

.PHONY: upgrade
upgrade: 
	@echo "$(COLOR_GREEN)Upgrading Terraform resources...$(COLOR_DEFAULT)"
	terraform init -upgrade

.PHONY: rmstate
rmstate: 
	@echo "$(COLOR_GREEN)Deleting remote state...$(COLOR_DEFAULT)"
	terraform state rm $(strip $(filter-out $@,$(MAKECMDGOALS)))

.PHONY: import
import: 
	@echo "$(COLOR_GREEN)Importing...$(COLOR_DEFAULT)"
	terraform import $(strip $(filter-out $@,$(MAKECMDGOALS)))

.PHONY: clean
clean: 
	@echo "$(COLOR_YELLOW)Cleaning up Terraform artifacts...$(COLOR_DEFAULT)"
	rm -rf ./.terraform
	rm -rf ./.terraform.lock.hcl
	rm -rf ./.infracost

.PHONY: tfsec
tfsec: 
	@echo "$(COLOR_YELLOW)Running tfsec...$(COLOR_DEFAULT)"
	docker run --rm -it -v "$(PWD):/src" aquasec/tfsec /src

.PHONY: infracost
infracost: cost

.PHONY: cost
cost:
	@echo "$(COLOR_YELLOW)Calculating estimated infrastructure costs with infracost...$(COLOR_DEFAULT)"
	docker run --rm -t -v "$(PWD)":/src -v ~/.config/infracost:/root/.config/infracost infracost/infracost breakdown --path "/src" --show-skipped

.PHONY: kics
kics:
	@echo "$(COLOR_YELLOW)Running KICS...$(COLOR_DEFAULT)"
	docker run --rm -t -v .:/path checkmarx/kics scan -p /path

.PHONY: semgrep
semgrep:
	@echo "$(COLOR_YELLOW)Running semgrep...$(COLOR_DEFAULT)"
	docker run --rm -t -v .:/src returntocorp/semgrep semgrep --config auto

.PHONY: megalint
megalint:
	@echo "$(COLOR_YELLOW)Running Mega Linter on the whole repository...$(COLOR_DEFAULT)"
	docker run --rm -e LOG_FILE="none" -e REPORT_OUTPUT_FOLDER='/tmp/' -v $(shell git rev-parse --show-toplevel):/tmp/lint oxsecurity/megalinter:v6 mega-linter-runner -p /tmp/lint/

.PHONY: docs
docs:
	@echo "$(COLOR_YELLOW)Generating Terraform documentation...$(COLOR_DEFAULT)"
	terraform-docs md ./ > README.md

.PHONY: tfk8s
tfk8s:
	@echo "$(COLOR_YELLOW)Converting K8s YAML (input.yaml) to Terraform resource (converted.tf)...$(COLOR_DEFAULT)"
	tfk8s -f input.yaml -o converted.tf

.PHONY: check-awscli-version
check-awscli-version:
	@echo "$(COLOR_YELLOW)Checking awscli version...$(COLOR_DEFAULT)"
	@aws --version 2>&1 | grep -q "aws-cli/.* Python/.*" && \
	aws --version 2>&1 | awk '{split($$2,ver,"."); if (ver[1] > 2) exit 0; else exit 1}' && \
	echo "${COLOR_GREEN}OK${COLOR_DEFAULT}" || \
	(echo "${COLOR_RED}Error: awscli version must be greater than 2.x${COLOR_DEFAULT}" && exit 1)

check-terraform-version:
	@echo "$(COLOR_YELLOW)Checking terraform version...$(COLOR_DEFAULT)"
	@terraform version | grep -q "Terraform v.*" && \
	terraform version | awk '{split($$2,ver,"."); if (ver[1] > 1 || (ver[1] == 1 && ver[2] >= 4 && ver[3] >= 2)) exit 0; else exit 1}' && \
	echo "${COLOR_GREEN}OK${COLOR_DEFAULT}" || \
	(echo "${COLOR_RED}Error: terraform version must be equal to or greater than 1.4.2${COLOR_DEFAULT}" && exit 1)

.PHONY: req
req: check-awscli-version check-terraform-version
	@echo "$(COLOR_YELLOW)Checking local dependency requirements...$(COLOR_DEFAULT)"

.PHONY: all
all: init validate fmt plan apply

.DEFAULT_GOAL := help
