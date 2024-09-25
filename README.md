# Antelope User Support

Welcome to the support portal for Antelope users! Here you will find the following resources:

 * [Installation Guide](INSTALL.md)
 * [User Guide](user_guide/README.md) with an overview of the technologies
 * [Demos](demos) in the form of jupyter notebooks
 * [Issues](issues) for flagging problems and reporting bugs
 * [gitter channel](https://matrix.to/#/#antelopepy-user-support:gitter.im) to ask questions or talk 
about LCA 

# Getting Started

Antelope is software for conducting ISO 14044 Life Cycle Assessment (LCA) analysis using a range of
data sources.  

The fastest way to get moving with Antelope is to install it without any extras and connect
to the [vault.lc blackbook server](https://vault.lc) as a guest.   Your usage will be limited
(to approximately 10 queries per hour) but the remedy for that is to create a free account!

```shell
$ python -m venv my_env
$ source my_env/bin/activate
(my_env) $ pip install antelope-core
```

> [!TIP]
> It's very useful to use [virtual environments](https://docs.python.org/3/library/venv.html) when using python so that you will always
> have everything you need for a given project.

Once you have it installed, you can simply run it. Start python, and then:

```python
import antelope_core
cat = antelope_core.LcCatalog()
cat.blackbook_guest('https://sc.vault.lc/')

```

## OK, but what then?
After that, there is a three-step process to using Antelope to conduct LCA:

1. Obtain access to a data resource
2. Use the [query interface](https://github.com/AntelopeLCA/user/blob/main/user_guide/catalog.md#queries) to retrieve references to LCA data objects
3. Ask the objects questions about themselves, and their relationships to one another.

The role of the query is to provide an abstraction to the LCA data.  The query is responsible for providing the user with *implementations* of the 
[Antelope interface specification][(https://antelopelca.github.io/antelope/interfaces/abstract.html).  Read more about how to [use LCA data](using_lca_data.md).
