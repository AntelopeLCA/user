# Installation

All the components of Antelope can be installed from PyPI (except this repo).  

Remember to always work in a virtual environment!

## Minimal installation

For background data, benchmarking, but no modeling to speak of, you can do straight
up LCA with `antelope-core`

```text
# pip install -r requirements-minimal.txt
antelope-core

```


## Modeling installation

For background data, benchmarking, and modeling. Supports some local data (notably, not XML)  

```text
# pip install -r requirements-modeling.txt
antelope-foreground

```

This comes with: `pydantic`, `requests`, and two of my utility packages: `xlstools` which
provides an `xlrd`-like interface for `openpyxl`; and `synonym-dict`, which is used to manage
sets of synonymous terms for objects.


## Background and LCI

If you are going to use local data sources, you will need to construct and invert LCI matrices:

```text
# pip install -r requirements-lci.txt
antelope-foreground
antelope-background

```
This adds `scipy` to the list of dependencies, which is very heavy.


## Ecospold and ILCD

If you are working with ecoinvent or ILCD data, you need XML.  You probably also will want to do
LCI.

```text
# pip install -r requirements-data.txt
antelope-foreground
antelope-background
lxml

```

## Reporting and Analysis

The most heavy-weight version of the code includes `antelope-reports`, which has tools for 
sophisticated scenario running, as well as charts and tables.  This will increase the download 
size by about 3x over background.

```text
# pip install -r requirements-analysis.txt
antelope-foreground
antelope-background
antelope-reports
lxml

```

This adds `pandas`, `matplotlib`, and `pydot`, along with all their associated requirements.

# The Test Suite and Demo environment

## The Test suite

`antelope-core` comes with a testing environment that validates key functions of the LCA machinery (only about 60% coverage, however).
The test suite cannot easily be run when the package is installed from pip-- instead, the solution (for now) seems to be to clone the
[antelope-core](https://github.com/AntelopeLCA/core) repository locally.  The `antelope-interface` and `antelope-background` repos are
both needed for the test suite to run, and they can still be installed using pip (although this will also result in `antelope-core`
being installed as a dependency-- the test suite can still be run from the github repo).

It's possible that I can render the tests runnable by switching to the `src/` layout and modifying the packaging machinery. I will 
take this on at some point but not now.

## The Demo environment
One of the features of the test suite is to install a persistent, local set of demonstration data using an ancient version of
USLCI in ecospold and OpenLCA JSON file formats.  In order for this to work, the test suite needs to know where to place the
persistent catalog.  This is specified by the environment variable `ANTELOPE_CATALOG_ROOT`; in its absence, the default value of 
`/data/LCI/cat-demo` is used.  If the directory doesn't exist the test suite will try to create it; on mac or linux systems, this 
will fail  the user somehow has permission to create that directory (e.g. if the user creates the directory `/data/` as superuser 
and gives themselves permission to write to it, then the test suite's directory creation will succeed). Otherwise, the user 
must set the environment variable prior to running the test.

## Running the tests / creating the demo environment

The test can be run as follows:

```shell
(my_env) $ pip install antelope-background
(my_env) $ git clone git@github.com:AntelopeLCA/core.git
(my_env) $ cd core
(my_env) $ ANTELOPE_CATALOG_ROOT=<specify your path here> python -m unittest
```

It will create the specified path and proceed to download the two USLCI repos from fixed links in Brandon Kuczenski's personal
Dropbox.  The tests should run and create the persistent catalog in the created path.

Please let me know if this works (via chat or email) or does not work (via opening an issue)! Thanks.


If the `antelope-background`
