# Installation

All the components of Antelope can be installed from PyPI (except this repo).  

Remember to always work in a virtual environment!

## Minimal installation

For background data, benchmarking, but no modeling to speak of, you can do straight
up LCA with `antelope-core`

```text
# requirements-minimal
antelope-core

```


## Minimal installation

For background data, benchmarking, and modeling. Supports some local data (notably, not XML)  

```text
# requirements-modeling
antelope-foreground

```

This comes with: `pydantic`, `requests`, and two of my utility packages: `xlstools` which
provides an `xlrd`-like interface for `openpyxl`; and `synonym-dict`, which is used to manage
sets of synonymous terms for objects.


## Background and LCI

If you are going to use local data sources, you will need to construct and invert LCI matrices:

```text
# requirements - LCI
antelope-foreground
antelope-background

```
This adds `scipy` to the list of dependencies, which is very heavy.


## Ecospold and ILCD

If you are working with ecoinvent or ILCD data, you need XML.  You probably also will want to do
LCI.

```text
# requirements-data
antelope-foreground
antelope-background
lxml

```

## Reporting and Analysis

The most heavy-weight version of the code includes `antelope-reports`, which has tools for 
sophisticated scenario running, as well as charts and tables.  This will increase the download 
size by about 3x over background.

```text
# requirements-analysis
antelope-foreground
antelope-background
antelope-reports
lxml

```

This adds `pandas`, `matplotlib`, and `pydot`, along with all their associated requirements.
