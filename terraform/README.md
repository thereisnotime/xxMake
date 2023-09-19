# terraform

Biased Makefile for easier Terraform project management, optimization, troubleshooting etc.

## Usage

Place it in your Terraform project folder next to your main.tf or add a template that references it from the root of your repository in case you have multiple projects.

### Infracost

If you want to use infracost, make sure to have the $HOME/.config/infracost/credentials.yml file with the API key inside.

## Installation

Just place the file in your project:

```bash
curl -s https://raw.githubusercontent.com/thereisnotime/xxMake/master/docker/Makefile -o Makefile
```

And do

```bash
make help
```

## Update

To update the Makefile from the current directory (or the template's source) you do:

```bash
make update-self
```

NOTE: GitHub has a 5 minute cache of raw files.

## Template

If you want to use it in a single repo and not have it copied few times you can put a template Makefile that will inherit it like this:

```make
#!make
include ../../Makefile
```
