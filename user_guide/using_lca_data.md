# Using LCA Data

The workflow for performing simple LCA activities is:

1. obtain access to a data **resource**
2. query the resource to receive **references** to LCA data objects (quantities, flows, processes, and fragments)
3. use the built-in methods of the references to find out information about the objects.

Here's a simple example from scratch, using `antelope-core`, without any local data:

### 0. Connect to authentication server server

> [!NOTE]
> https://sc.vault.lc/ is the canonical authentication server (nicknamed blackbook) run by [vault.lc](https://vault.lc/), the life cycle
> data repository hosted by Scope 3 Consulting LLC. At the moment (Fall 2024) it hosts a selection of free data taken
> from the [Federal LCA Commons](https://lcacommons.gov) as well as the reference [LCIA implementation](https://github.com/GreenDelta/data/)
> prepared by GreenDelta for its free OpenLCA tool.  

```python
from antelope_core import LcCatalog
cat = LcCatalog()
cat.blackbook_guest('https://sc.vault.lc')
list(cat.blackbook_origins)  # list the resources being made available by the blackbook server to the guest user
```

> [!TIP]
> The blackbook server accepts a fixed number of queries from guest accounts based on IP address.  The query counter resets every hour.

### 1. Obtain a data resource

```python
# Obtain a data resource for USEEIO
useeio = cat.get_blackbook_resources('lcacommons.useeio.2.0.1')
useeio
'''
[LcResource(lcacommons.useeio.2.0.1, dataSource=https://bk.vault.lc/:XdbClient, ['index', 'quantity', 'basic', 'background', 'exchange'] [50])]
'''
```

The resource's description shows that it provides access to all five READ-ONLY interfaces: 'index', 'quantity', 'basic', 'background', 'exchange'.  
Note that the `get_blackbook_resources` command returns
a *list* of resources, although in this case the list has only one entry.  In general, many different resources may be required to implement
the various interfaces.

You can now construct a query to access this resource.  If there are multiple resources for the same origin, the query will abstract those away, 
so that the user (ideally) does not have to consider them.

```
q_useeio = cat.query(useeio[0].origin)
```

### 2. Ask a question

The list of questions that you many ask is spelled out in the [Antelope interface specification](https://antelopelca.github.io/antelope/interfaces/abstract.html)

```python
list(q_useeio.lcia_methods())  # a search method provided by the index interface
'''
GET https://bk.vault.lc/lcacommons.useeio.2.0.1/lcia_methods.. 200 [0.44 sec]
Out[13]: 
[<antelope.refs.quantity_ref.QuantityRef at 0x7030fcbec6a0>,
 <antelope.refs.quantity_ref.QuantityRef at 0x7030fcbee320>,
 <antelope.refs.quantity_ref.QuantityRef at 0x7030fcbed210>,
 <antelope.refs.quantity_ref.QuantityRef at 0x7030fcbed6f0>,
 <antelope.refs.quantity_ref.QuantityRef at 0x7030fcbeddb0>]
'''
```

Antelope provides a simple interactive tool called `enum`, which is useful for inspecting lists of objects.

```
from antelope import enum
lcias = enum(_)  # the underscore '_' is python shorthand for "the previous output"
'''
 [00] [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method]
 [01] [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Value Added [$] [USEEIO - LCIA Method]
 [02] [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial Municipal Solid Waste [kg] [USEEIO - LCIA Method]
 [03] [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Jobs Supported [jobs] [USEEIO - LCIA Method]
 [04] [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial RCRA Hazardous Waste [kg] [USEEIO - LCIA Method]
'''
```
The objects that returned are *entity references*, specifically `QuantityRef`s.  These quantities correspond to LCIA methods because
they have an `Indicator` property defined.  The 'ref' (entity reference) *contains* the query that was used to retrieve it, so you can ask the 
ref questions directly, and it will forward them on to the query.

```
lcias[0].show()
'''
QuantityRef catalog reference (21f680ec-d6a2-30f6-90ae-040e50aabed3)
origin: lcacommons.useeio.2.0.1
   UUID: 21f680ec-d6a2-30f6-90ae-040e50aabed3
   Name: USEEIO - LCIA Method, Commercial Construction and Demolition Debris
Comment: 
referenceUnit: kg
==Local Fields==
        Indicator: kg
         Category: Commercial Construction and Demolition Debris
           Method: USEEIO - LCIA Method
MethodDescription: Indicators generated specifically for use in USEEIO models
   UnitConversion: {'kg': 1.0}
         Synonyms: []
 blackbook_origin: lcacommons.useeio.2.0.1
'''
_ = enum(lcias[0].factors())  # factors() is part of the quantity interface
# used in this fashion, the underscore '_' suppresses the display of the output to the screen
'''
GET https://bk.vault.lc/lcacommons.useeio.2.0.1/21f680ec-d6a2-30f6-90ae-040e50aabed3/factors.. 200 [0.26 sec]
Imported 7 factors for [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method]
 [00]      1 [GLO] [kg / kg] Asphalt Pavement: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [01]      1 [GLO] [kg / kg] Asphalt Shingles: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [02]      1 [GLO] [kg / kg] Bricks: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [03]      1 [GLO] [kg / kg] Concrete: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [04]      1 [GLO] [kg / kg] Gypsum Drywall: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [05]      1 [GLO] [kg / kg] Metal: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
 [06]      1 [GLO] [kg / kg] Wood: None (USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method])
'''
```

### 3. Perform analysis by combining two or more LCA entity references

In order to use an LCIA method, it's generally necessary to have a process model. 
```
hotels = enum(q_useeio.processes(name='hotel')  # a search method provided by the index interface
'''
GET https://bk.vault.lc/lcacommons.useeio.2.0.1/processes.. 200 [0.03 sec]
 [00] [lcacommons.useeio.2.0.1] Hotels and campgrounds [United States]
 [01] [lcacommons.useeio.2.0.1] Gambling establishments (except casino hotels) [United States]
'''
result = hotels[0].bg_lcia(lcias[0])  # bg_lcia is, believe it or not, part of the basic interface
'''
GET https://bk.vault.lc/lcacommons.useeio.2.0.1/13565eb5-bfe7-3340-a745-a04ded146187/lcia/21f680ec-d6a2-30f6-90ae-040e50aabed3.. 200 [1.38 sec]
'''
result.total()  # an LciaResult is part of the antelope core implementation
'''
0.003629042358293406
'''
result.show_details()
'''
[lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method] kg
------------------------------------------------------------

[lcacommons.useeio.2.0.1] Hotels and campgrounds [United States]:
    0.00161 =          1  x    0.00161 [GLO] Concrete, None
    0.00122 =          1  x    0.00122 [GLO] Wood, None
   0.000283 =          1  x   0.000283 [GLO] Gypsum Drywall, None
   0.000249 =          1  x   0.000249 [GLO] Asphalt Pavement, None
    0.00013 =          1  x    0.00013 [GLO] Asphalt Shingles, None
   0.000104 =          1  x   0.000104 [GLO] Bricks, None
   3.74e-05 =          1  x   3.74e-05 [GLO] Metal, None
   0.00363 [lcacommons.useeio.2.0.1] USEEIO - LCIA Method, Commercial Construction and Demolition Debris [kg] [USEEIO - LCIA Method]
'''
```

### That's all for now

On your own, you can explore other features of the Antelope specification, like asking a process for its `inventory` or for individual
`exchanges` or `exchange_values`, or breaking the inventory up into `dependencies` (linked nonreference flows), `cutoffs` (unlinked nonreference
flows) and `emissions` (nonreference flows that terminate in environmental contexts).

