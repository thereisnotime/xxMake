#!make
#########################################
# TINT - Docker Makefile
#########################################

# Define live updates
MAKE_REPOSITORY := https://raw.githubusercontent.com/thereisnotime/xxMake/master/docker/Makefile
MAKE_VERSION := 1.10

# Project Configuration
IMAGE_NAME := tempimage
IMAGE_TAG := latest
REGISTRY := hub.docker.com

# Define colors
COLOR_DEFAULT := \033[0m
COLOR_GREEN := \033[0;32m
COLOR_YELLOW := \033[0;33m

.PHONY: help
help:
	@printf "$(COLOR_GREEN)Available targets:$(COLOR_DEFAULT)\n\
		$(COLOR_YELLOW)all$(COLOR_DEFAULT)         This will lint, build and test\n\
		$(COLOR_YELLOW)test$(COLOR_DEFAULT)        Test the latest built image\n\
		$(COLOR_YELLOW)build$(COLOR_DEFAULT)       Build Docker image\n\
		$(COLOR_YELLOW)build-run$(COLOR_DEFAULT)   Build and run Docker container and remove it after exit\n\
		$(COLOR_YELLOW)run$(COLOR_DEFAULT)         Run Docker container\n\
		$(COLOR_YELLOW)push$(COLOR_DEFAULT)        Push Docker image to the registry\n\
		$(COLOR_YELLOW)pull$(COLOR_DEFAULT)        Pull Docker image from the registry\n\
		$(COLOR_YELLOW)test$(COLOR_DEFAULT)        Run tests on Docker container\n\
		$(COLOR_YELLOW)clean$(COLOR_DEFAULT)       Remove Docker image and containers\n\
		$(COLOR_YELLOW)dive$(COLOR_DEFAULT)        Analyze image disk usage with dive\n\
		$(COLOR_YELLOW)lint$(COLOR_DEFAULT)        Run all linter\n\
		$(COLOR_YELLOW)megalint$(COLOR_DEFAULT)    Run megalinter on the whole repository\n\
		$(COLOR_YELLOW)trivy$(COLOR_DEFAULT)       Run trivy on the built image\n\
		$(COLOR_YELLOW)update-self$(COLOR_DEFAULT) Updates Makefile to latest\n"

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
		echo "$(COLOR_YELLOW)Updating Makefile... $(COLOR_GREEN)Done!$(COLOR_DEFAULT)"; \
		mv "$$MAIN_MAKEFILE_DIR.new" "$$MAIN_MAKEFILE_DIR"; \
	else \
		echo "Current version is $(COLOR_GREEN)up-to-date$(COLOR_DEFAULT). No need for update."; \
		rm "$$MAIN_MAKEFILE_DIR.new"; \
	fi

.PHONY: all
all: lint build test
	@echo "$(COLOR_YELLOW)Building...$(COLOR_DEFAULT)"

.PHONY: clean
clean:
	@echo "$(COLOR_YELLOW)Cleaning up artifacts...$(COLOR_DEFAULT)"
	# TODO: Implement cleanup

.PHONY: test
test:
	@echo "$(COLOR_YELLOW)Testing (needs a built image)...$(COLOR_DEFAULT)"
	docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock -v  "$(PWD)/tests":"/tests" -w "$(PWD)" gcr.io/gcp-runtimes/container-structure-test test --image $(IMAGE_NAME):$(IMAGE_TAG) --config /tests/test.yml

.PHONY: build
build:
	@echo "$(COLOR_YELLOW)Building Docker image...$(COLOR_DEFAULT)"
	docker build -t $(IMAGE_NAME):$(IMAGE_TAG) .

.PHONY: run
run:
	@echo "$(COLOR_YELLOW)Running Docker container...$(COLOR_DEFAULT)"
	docker run -it $(IMAGE_NAME):$(IMAGE_TAG)

.PHONY: build-run
build-run: build
	@echo "$(COLOR_YELLOW)Running Docker container with --rm flag...$(COLOR_DEFAULT)"
	docker run --rm -it $(IMAGE_NAME):$(IMAGE_TAG)

.PHONY: push
push:
	@echo "$(COLOR_YELLOW)Pushing Docker image to the registry...$(COLOR_DEFAULT)"
	docker tag $(IMAGE_NAME):$(IMAGE_TAG) $(REGISTRY)/$(IMAGE_NAME):$(IMAGE_TAG)
	docker push $(REGISTRY)/$(IMAGE_NAME):$(IMAGE_TAG)

.PHONY: dive
dive:
	@echo "$(COLOR_YELLOW)Analyzing build with dive...$(COLOR_DEFAULT)"
	docker run --rm -it -e CI=true -v /var/run/docker.sock:/var/run/docker.sock -v "$(PWD)":"$(PWD)" -w "$(PWD)" wagoodman/dive:latest build -t $(IMAGE_NAME):$(IMAGE_TAG) .

.PHONY: pull
pull:
	@echo "$(COLOR_YELLOW)Pulling Docker image from the registry...$(COLOR_DEFAULT)"
	docker pull $(REGISTRY)/$(IMAGE_NAME):$(IMAGE_TAG)

.PHONY: lint
lint: megalint
	@echo "$(COLOR_YELLOW)Linting...$(COLOR_DEFAULT)"

.PHONY: megalint
megalint:
	@echo "$(COLOR_YELLOW)Running Mega Linter on the whole repository...$(COLOR_DEFAULT)"
	docker run --rm -e LOG_FILE="none" -e DISABLE_LINTERS=SPELL_CSPELL -e REPORT_OUTPUT_FOLDER='/tmp/' -v $(shell git rev-parse --show-toplevel):/tmp/lint oxsecurity/megalinter:v6 mega-linter-runner -p /tmp/lint/

.PHONY: trivy
trivy:
	@echo "$(COLOR_YELLOW)Running Trivy on the whole repository (needs a built image)...$(COLOR_DEFAULT)"	
	docker run --rm  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --exit-code 0 $(IMAGE_NAME):$(IMAGE_TAG)

.DEFAULT_GOAL := help
