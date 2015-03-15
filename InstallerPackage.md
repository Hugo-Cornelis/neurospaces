[Index](Index.md)

[InstallationOnMAC](InstallationOnMAC.md)

[NeurospacesRepositoryAndTheInstaller](NeurospacesRepositoryAndTheInstaller.md)

[Autogen](Autogen.md)

G3 Doc: installer-package

# Introduction #

Developer utilities that comply with Neurospaces development standards.  The most important one is the 'neurospaces\_build' script used for automated software installation and maintenance on a set of software packages.  Because this script has many options, most common operations are provided using frontends.  Other scripts are related to version identification of the software, and source code documentation comments.

Some of the utilities currently depend on monotone for source code version control due to their configuration.  It is possible to work with other version control systems.

# Utilities #

> Main driver script:
  * neurospaces\_build: perform various build operations, do 'neurospaces\_build --help' to see how it works.  Also look at ReleaseProcedure.

> Other utilities: these utilities take optional arguments of '--regex' to select what packages they operate on, and '--verbose' to run the command in a more verbose mode.

  * neurospaces\_packages: show what packages are enabled on your local machine.
  * neurospaces\_versions: shows what versions of the neurospaces packages are installed.
  * neurospaces\_create\_directories: create the correct directory layout required for Neurospaces development.

  * neurospaces\_serve: starts serving the source code repositories such that other people can pull and sync to your machine (note that this locks all your databases).
  * neurospaces\_pull: download the source code from a repository.
  * neurospaces\_status: check for local source code modification (no network required).
  * neurospaces\_sync: synchronize local source code modification with a repository.
  * neurospaces\_update: makes the local source code up to date using the repositories locally stored on your PC (so this is a local operation).

  * neurospaces\_configure: (re)configures the simulator software (requires packages to be installed already).
  * neurospaces\_install: install the simulator software.
  * neurospaces\_uninstall: uninstalls the simulator software (including the installer scripts, to reinstall you will have to go to the installer source code directory and run 'make && sudo make install').
  * neurospaces\_check: check for correctness of the installed software.
  * neurospaces\_clean: clean source code directories.

  * neurospaces\_docs: builds documentation on your local machine.
  * neurospaces\_website\_prepare: prepare a version of the website on your local pc, and optionally upload it.

  * [neurospaces\_cron](neurospaces_cron.md): a cron job based tester script.

Soon also:

  * neurospaces\_download: download the simulator from a central archive.

For updating of version keywords the following are invoked automatically by the neurospaces\_build script:
  * release\_extract: extract release information from a monotone repository.  When a Tag is set, that will be the result, otherwise the SHA of the current base revision will be the result.
  * release\_expand: do keyword expansion, see the manpage in the source for more details.

Other:

  * mcad2doxy: convert obsoleted multicad documentation to doxygen format, this has been used to convert the Heccer developer documentation to doxygen format.  Other packages will follow.
  * [release-expand](ReleaseExpand.md): for configurable keyword expansion in source code and documentation.

# Examples #

  * See also InstallingFromScratch
  * building a release for the model-container package (see also ReleaseProcedure):
    1. Tag the code, eg. we use the tag 'build-11'
```
neurospaces_build --tag build-11 --verbose --verbose --verbose --no-compile --no-configure --no-install --regex model-container --developer
```
    1. Build the release for the tagged code:
```
neurospaces_build --dist --verbose --verbose --verbose --no-compile --no-configure --no-install --regex model-container --developer
```
    1. Or with one command line:
```
neurospaces_build --tag build-11 --dist --verbose --verbose --verbose --no-compile --no-configure --no-install --regex model-container --developer
```
    1. Upload the tarballs:
```
neurospaces_build --src-tag build-11 --upload-server ftp://upload.sourceforge.net/incoming --verbose --verbose --verbose --no-compile --no-configure --no-install --regex model-container --developer
```
    1. Do not forget to edit release notes, tag the files as 'Any', 'Source .gz', etc.  Due to the crappy sourceforge interfaces, this has to be done manually (any one any ideas?).

# Other Examples #

  1. Figuring out what packages are known:
```
neurospaces_build --help-packages
```
  1. Figuring out what operations on those packages are known:
```
neurospaces_build --help-operations
```
  1. Figuring out what actions are taken by default:
```
neurospaces_build --help
```
> > and inspect the flags at the end of the output.  At this time, --configure, --compile and --install are the default actions.
  1. Figuring how operations are mapped to the packages:
```
neurospaces_build --help-all
```
  1. Figuring out what actions are taken after options have been given, eg.
```
neurospaces_build --distcheck --help
```
> > will enable the distcheck target of the makefiles, in addition to the default actions.
  1. Figuring out what shell commands will be run after options have been given, without really running the commands:
```
neurospaces_build --verbose --distcheck --dry-run --developer
```
  1. Uninstalling 4 packages on your developer machine:
```
neurospaces_build --verbose --verbose --verbose --no-compile --no-configure --uninstall --regex '(model-container|heccer|ssp|studio)' --developer
```
  1. Building checked releases for four packages:
```
neurospaces_build --verbose --verbose --verbose --no-compile --no-configure --no-install --distcheck --regex '(model-container|heccer|ssp|studio)' --developer
```
  1. After a modification of the installer package, reinstall it:
```
neurospaces_build --enable installer --regex installer --developer --verbose --verbose --verbose
```
  1. Releasing the installer package on sourceforge:
```
neurospaces_build --tag build-25 --dist --src-tag build-25 --upload-server ftp://upload.sourceforge.net/incoming --enable installer --regex '(installer)' --developer --verbose
```