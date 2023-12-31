# docker

Biased Makefile for easier Docker project management, optimization, troubleshooting etc.

## Usage

Place it in your Docker project folder next to your main.tf or add a template that references it from the root of your repository in case you have multiple projects.

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

## Todos

The following tasks are in the roadmap:

- [ ] Add option to work with a local .env that contains a specific docker container registry configuration.
- [ ] Finish the open TODO's in the Makefile.
- [ ] Add option to autogenerate yaml files for Docker tests with ChatGPT if there is a key provided.
