# The Antelope Catalog

```python
>>> from antelope_foreground import ForegroundCatalog
>>> cat = ForegroundCatalog('/data/LCI/cat-demo')  # this lives on my local computer
>>> cat.show_interfaces()
'''
lcacommons.uslci.fy22.q3 [background, basic, exchange, index]
lcia.ipcc.2007.traci21 [basic, index, quantity]
local.ecoinvent.3.6.cutoff [basic, exchange]
local.ecoinvent.3.6.cutoff.index.20240423 [background, index]
local.ecoinvent.3.7.1.apos [basic, exchange]
local.ecoinvent.3.7.1.apos.index.20210507 [background, basic, index]
local.lcia.traci.2.1 [basic, index, quantity]
local.qdb [basic, index, quantity]
local.uslci.ecospold [basic, exchange, quantity]
local.uslci.ecospold.index.20240815 [background, basic, index]
local.uslci.olca [basic, exchange, quantity]
local.uslci.olca.index.20240815 [background, basic, index]
olca.alt [basic, exchange]
test [basic, foreground, index, quantity]
'''
```


The focal point of an Antelope session is a [catalog](https://antelopelca.github.io/core/reference/catalog/catalog/)
which stores a collection of [resources](https://antelopelca.github.io/core/reference/lc_resource/). The
Each resource describes a particular data collection that has been created and released by ... someone.

A resource has an **origin**, which is a hierarchical semantic reference.  This means that it

1. is both human and machine readable
2. its contents should be self-descriptive and meaningful
3. the association between an origin and a data source *should* be controlled *by* the data owner.

In the output of the `show_interfaces()` command above, you see a list of origins, each one having 
its own list of [interfaces](interfaces.md) it supports.

## Origins Example

```
local.uslci.ecospold [basic, exchange, quantity]
local.uslci.ecospold.index.20240815 [background, basic, index]
local.uslci.olca [basic, exchange, quantity]
local.uslci.olca.index.20240815 [background, basic, index]
```
Here I have four different origins, each of which supports five interfaces (the `basic` interface
is listed twice for each).  By reading the origins, I can infer that:
 - the data are stored locally
 - the data describe the USLCI database
 - the data are in (respectively) ecospold and OpenLCA format

It so happens that these resources are the ones configured by the `unittest` routine built into 
`antelope-core`.  When [antelope_core.data_sources.tests.test_aa_local.LocalCatalog](https://github.com/AntelopeLCA/core/blob/master/antelope_core/data_sources/tests/test_aa_local.py) is run as 
a test, it will configure these resources (and others) in a persistent location configured by
the environment variable `ANTELOPE_CATALOG_ROOT` (which is `/data/LCI/cat-demo` by default and
on my system).  Specifically, it will download source data from a special link in my personal Dropbox
account.

> [!NOTE]
> For testing consistency, these resources use versions of the USLCI repo that are 
> *out of date* and originate from 2018, before NREL began to curate the daabase
> more carefully.  The testing configuration handles all required allocation and
> flow property settings.

The download will only contain the raw data (a .ZIP of ecospold xml files in the ecospold case, a
.ZIP of JSON files in the OpenLCA case).  These files  are the source of all documentary (`basic`),
inventory (`exchange`) and flow property (`quantity`) data in the resource, and these are the 
interfaces they implement.

The derived interfaces (`index` and `background`) can be created from the raw data by reading them
into an index, and then performing a [partial ordering](https://link.springer.com/article/10.1007/s11367-015-0972-x) 
via the Tarjan algorithm.  This task is also performed by the test routine and creates *new* 
resources, namely the index and background archives.  These resources can be accessed via a **query**.

## Queries

A query is the way you ask the catalog for information.  Queries are directed at an *origin* and
link to specific interfaces.  The list of *things* you can query is spelled out in the 
[antelope interface specification](https://antelopelca.github.io/antelope/interfaces/abstract.html).

The way you use them is like this:

```python
q_olca = cat.query('local.uslci.olca')
q_olca.count('process')  # a count query in the index interface
'''
767
'''
softwoods = list(q_olca.flows(name='softwood'))  # a search query in the index interface
len(softwoods)
'''
96
'''
```

The abstract query implements a stub for every method in the interface, directing it to a `_perform_query` method
that must be filled out by an implementation.  This implementation, a [CatalogQuery](https://antelopelca.github.io/core/reference/catalog_query/)
is responsible for producing an **implementation** for whatever interface can answer the query.

If we re-run the queries with debugging turned on:

```python
q_olca.on_debug()
q_olca.count('process') 
'''
CatalogQuery Performing count query, origin local.uslci.olca, iface index
CatalogQuery yielding IndexImplementation for local.uslci.olca.index.20240815 (/data/LCI/cat-demo/index/ef42cf8f53552305a9d267d00e965d22fe6a185b.json.gz)
CatalogQuery Attempting count query on iface IndexImplementation for local.uslci.olca.index.20240815 (/data/LCI/cat-demo/index/ef42cf8f53552305a9d267d00e965d22fe6a185b.json.gz)
767
'''
```

Notice that the query has found an implemention that is **more specific** than the request (the
origin that was requested was `local.uslci.olca` but the resource that answered it was 
`local.uslci.olca.index.20240815`). This is a feature of the core implementation.  In fact, a 
single origin can have many resources associated with it; these are ordered by a priority setting.

## Sum up

In this guide we learned about how the Antelope catalog organizes data resources, and how a user
can use the catalog to produce query objects that can answer questions.

In the next chapter, we will see how we can use these resources to conduct LCA.

[Go to the next chapter](using_lca_data.md)
