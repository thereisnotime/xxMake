# xxMake

A biased Makefile collection for various use cases.

[![Lint Makefiles](https://github.com/thereisnotime/xxMake/actions/workflows/ci.yml/badge.svg)](https://github.com/thereisnotime/xxMake/actions/workflows/ci.yml)

## Features

- Autoupdate from the repository.
- Targets to help troubleshooting, management and operation.

## Goals and Philosophy

1. Makefiles should be self-contained.
2. Makefiles should have a help menu.
3. Makefiles should work with Docker containers where they use third party tools (other than busybox etc.).
4. Makefiles should can be biased if the bias implements a good practice.

## Requirements

Most of the requirements are:

- git
- curl
- Make
- Docker (for some of the fancier targets)
- grep
- awk
- sed
