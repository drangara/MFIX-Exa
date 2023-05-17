# MFIX-Exa
This is the public face of the MFIX-Exa project, hosting the documentation,
gallery of results, etc. The website is: https://amrex-codes.github.io/MFIX-Exa/

## Editing

The documentation source is in `docs/source/` and the html source for the cover
page is in `docs/webroot/`.

:warning: **Do not push any edits to the `gh-pages` branch** as these will be
overwritten. Instead, commit your changes to the `master` branch, and the
`gh-pages` branch will be updated automatically a few minutes later.

### Documentation

The documentation is built using
[sphinx](https://www.sphinx-doc.org/en/master/). The `docs/source/config.py`
file contains the settings for building this documentation. The documentation
can be built locally by:

1. Install sphinx:

```shell
> pip install sphinx sphinx_rtd_theme sphinx-tabs
```

2. Build the documentation:

```shell
> mkdir build
> sphinx-build -b html docs/source_docs/ build
```

The HTML pages will now be located in the `build` directory. Open the
`index.html` with your favorite browser:

```shell
> firefox build/index.html
```

### Doxygen

Whenever the online documentation is updated, a fresh doxygen will be generated
also. Note that the configuration in `docs/doxygen/doxygen.conf` specifically
prevent source code from being displayed. This is by design. If you want to
create your own private doxygen containing the source code also, please
configure and run the `doc/Doxyfile.in` in your clone of the MFiX-Exa repo.
