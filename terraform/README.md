# terraform

Biased Makefile for easier Terraform project management, optimization, troubleshooting etc.

## Usage

Place it in your Terraform project folder next to your main.tf or add a template that references it from the root of your repository in case you have multiple projects.

### Infracost

If you want to use infracost, make sure to have the $HOME/.config/infracost/credentials.yml file with the API key inside.

## Installation

Just place the file in your project:

```bash
curl -o https://raw.githubusercontent.com/thereisnotime/xxMake/master/terraform/Makefile
```

And do

```bash
make help
```

## Update

NOTE: GitHub has a 5 minute cache of raw files.

Do:

```bash
make update-self
```
