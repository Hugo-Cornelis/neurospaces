G3 Doc: installation-osx

[Index](Index.md)

[InstallerPackage](InstallerPackage.md)

[NeurospacesRepository](NeurospacesRepository.md)

# Introduction #

This page explains how to do a developer installation of GENESIS on a MAC.  Almost the same installation procedure for Neurospaces on a regular PC works on the MAC.


## Installing Dependencies ##

Note that some installation must be done from the terminal. The terminal can be found in the Applications/Utilities folder. Would be a good idea to drag the terminal to the doc for easy access.

  * Install monotone, this is freely available as a package from the monotone website http://monotone.ca/.
  * Install Xcode, the Apple Development Toolset. It is available disk 2 of Mac OSX under optional install. It is also available from the Apple Developer Website here http://developer.apple.com/technology/xcode.html you will need a valid Apple ID to log in and access the developer page. _Note: It is not advised to use the compilers available from macports._
  * Install the following perl dependencies via CPAN:
    1. YAML
    1. Inline
    1. RecDescent
    1. Expect (and its dependency perl-IO)
    1. ExtUtils
    1. Data::Comparator
    1. File::Find::Rule

## Installation ##

Once these dependencies have been met you can pull source code from the GENESIS repository and do a build on your machine with the following commands:

  * _neurospaces\_create\_directories_
  * _neurospaces\_pull_
  * _neurospaces\_update_
  * _neurospaces\_configure_
  * _neurospaces\_install (requires admin password)_

Once these have completed you can test your installation by performing this command:

> _neurospaces\_check_

This will perform a series of regression tests that will indicate any inconsistencies with your installation.