# You can set these variables from the command line.
SPHINXOPTS    =
SPHINXBUILD   = python -msphinx
SOURCEDIR     = source_docs
BUILDDIR      = webroot

all: html webroot/doxygen
	mv webroot/html webroot/docs_html
# Sphinx is set up to treat its build directory as the webroot, hence the
# .nojekyll file is in the wrong place
	mv webroot/docs_html/.nojekyll webroot

help:
	@$(SPHINXBUILD) -M help "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

.PHONY: help Makefile

# Catch-all target: route all unknown targets to Sphinx using the new
# "make mode" option.  $(O) is meant as a shortcut for $(SPHINXOPTS).
%: Makefile
	@$(SPHINXBUILD) -M $@ "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

webroot/doxygen:
	git clone "https://gitlab-ci-token:$(GITLAB_TOKEN)@mfix.netl.doe.gov/gitlab/exa/mfix.git" doxygen/mfix
	cd doxygen && doxygen doxygen.conf
	mv doxygen/html $@
