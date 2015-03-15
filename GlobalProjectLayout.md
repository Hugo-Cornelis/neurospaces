[Index](Index.md)

G3 Doc: developers-intro.tex

# Developing Sources #

This page describes the directory structure I use to do Neurospaces development.  Also the general workflows should be described here.

[The Neurospaces project](http://www.neurospaces.org/) develops several software components useful for neural simulations using models based on experimental data:

  1. Each software component can have implementations in several programming languages (e.g. there is a java based and C based implementation of the [model-container component](http://www.neurospaces.org/#section-neurospaces)).
  1. Each software component has several resources: source code, documentation, additional tools etc.
  1. Each software component can have experimental features, not part of an official distribution.  We call this a branch of the source code (it is irrelevant if such a branch is present in a version control system or not).

Conclusions:

  * the source code of a software component is normally put in a directory `$HOME/neurospaces_project/<package-name>/source/<programming language>/snapshots/<branch name>/`.
  * Other resources are put in other directories: e.g. docs not part of the distribution can be put under `$HOME/neurospaces_project/<package-name>/docs/`.
  * Archived patches are put in `$HOME/neurospaces_project/<package-name>/source/<programming language>/patches/`

The InstallerPackage has builtin support for this directory structure when working in developer mode (using its `--developer` option).

# Configuration #

Configuration comes in the `/etc/neurospaces/` directory.

Every software component or tool has its own subdirectory.  E.g. the `morphology2ndf` convertor, part of the [model-container](http://www.neurospaces.org/#section-neurospaces), has its default configuration in `/etc/neurospaces/morphology2ndf`.  Configuration files are specified in the [YAML format](http://www.yaml.org/), because scripting languages like [perl](http://www.perl.org/) and [python](http://www.python.org/) have builtin support to process YAML, and because YAML is more readable than XML.

Configuration is normally processed as follows:

  1. Every tool has builtin default configuration.  Every tool must be able to work in an intuitive way when no other configuration is found except the default builtin configuration.
  1. Every tool reads its configuration from `/etc/neurospaces/<tool-name>/`.  This is merged with default configuration, possibly overriding default configuration.
  1. Every tool accepts additional configuration from the user, via command line options or otherwise, again possibly overwriting configuration settings generated in the previous step.

The merging algorithm is defined in [the Data-Utilities package](http://search.cpan.org/dist/Data-Utilities/), available from [CPAN](http://www.cpan.org/).

There is a default configuration available in [the configurator package](http://neurospaces.sourceforge.net/#section-configurator).