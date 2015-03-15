[Index](Index.md)

PrerequisiteScript ListOfDependencies

G3 Doc: install-procedure.tex

# Introduction #

This page assumes that the target PC has all [dependencies](ListOfDependencies.md) installed.  You can the proceed by downloading the packages from http://sourceforge.net/project/showfiles.php?group_id=162899.  The download can be automated by the InstallerPackage.  Alternatively, you can checkout the code from the [monontone](http://monotone.ca/) [repositories](NeurospacesRepository.md).


All Neurospaces packages can be downloaded and installed by themselves, based on autotools ('./configure', 'make', 'sudo make install').  There is a small issue with the Heccer package: its compilation depends on the NMC to be installed.

The order of installation is:

  * [Installer](http://www.neurospaces.org/#section-installer-tools) (optional)
  * [NMC (model-container)](http://www.neurospaces.org/#section-neurospaces)
  * [Heccer](http://www.neurospaces.org/#section-heccer)
  * [SSP](http://www.neurospaces.org/#section-ssp)
  * [Studio](http://www.neurospaces.org/#section-studio)
  * [Project-Browser](http://www.neurospaces.org/#section-simulation-browser)

The most easy way to do the installation, is by using the package installer, see below.

# For a user #

Using the install script of the InstallerPackage:

  * Install the common Neurospaces packages (at this moment, the model container, heccer, ssp and the studio), from downloads.sourceforge.net, distribution build-37 (**please, modify 'build-37' to be the latest release available for download, at present it is 'python-5'**):
```
neurospaces_build --download-server downloads.sourceforge.net --src-tag build-37 --src-dir  /tmp/neurospaces/downloads --verbose --verbose --verbose
```
> > Add a couple of '--verbose' options to see what is going on.
  * To uninstall you can use the same command line, and use the '--uninstall' option:
```
neurospaces_build --download-server downloads.sourceforge.net --src-tag build-37 --src-dir /tmp/neurospaces/downloads --verbose --verbose --verbose --uninstall --no-configure --no-compile --no-install
```
> > Note that you must add the '--no-compile' option, otherwise things might break because of the compilation dependencies between the packages.
  * Or if you the sources still in your filesystem after a previous download:
```
neurospaces_build --no-compile --uninstall --src-tag build-37 --src-dir /tmp/neurospaces/downloads --verbose --verbose --verbose
```
> > Note that you must add the '--no-compile' option, otherwise things might break because of the compilation dependencies between the packages.
  * For each of the above command lines, it is possible to select individual packages using the `--regex` options.  Its default value is `--regex 'model-container|heccer|ssp|studio'`.

# For a developer #

Using the install script of the InstallerPackage:

  1. Understand how and why it works for a regular user.
  1. use the following commands:
```
# download, install, check
neurospaces_build --download-server downloads.sourceforge.net --check --regex 'model-container|heccer|ssp|studio' --src-tag build-37 --src-dir /tmp/neurospaces/downloads --verbose --verbose --verbose

# uninstall
neurospaces_build --no-compile --uninstall --regex 'model-container|heccer|ssp|studio' --src-tag build-37 --src-dir /tmp/neurospaces/downloads --verbose --verbose --verbose

# reinstall the things you were working on, developing
neurospaces_build --regex 'model-container|heccer|ssp|studio' --verbose --verbose --developer --verbose
```