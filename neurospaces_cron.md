G3 Doc: neurospaces-cron

# Introduction #

The neurospaces\_cron script is part of the [InstallerPackage](InstallerPackage.md), its function is to perform sanity checks for communicating with a [NeurospacesRepository](NeurospacesRepository.md), system installation, and running tests to see that the software packages are properly working. This page is here to serve as a guide to show how to use the script, set up a cronjob and how to avoid some common pitfalls.

---

# Running #

The neurospaces\_cron script can be run by invoking "neurospace\_cron" from the command line after installing the neurospaces installer package. There are command line arguments available to customize aspects of the cronjob:

  * outputdir - _This tells neurospaces\_cron what directory to write its output log to._
  * config - _Reads in a YAML configuration file for setting parameters._


---
# Configuration File #

A configuration file is a YAML with values set for the following optional parameters:

  * MAILTO - _An email recipient to send an email to on completion of the job_
  * MAILFROM - _A desired email address to send a completion email from._
  * OUTPUTDIR - _A directory to write the output log data._
  * URL - _An http link which serves the test harness output._
  * DOXYGENOUTPUT - _The local directory which holds the doxygen html output._
  * DOXYGENURL - _An http link which serves the doxygen output._
  * REGTESTOUTPUT - _The local directory which contains the output of the regression test html._
  * REGTESTURL - _An http link which serves the regression test html output._
  * LOGROTATE - _The number of output logs to rotate on successive runs._

Example configuration file`:`
```
---
MAILTO: my@email.com
MAILFROM: g3tester@repo-genesis3.cbi.utsa.edu
OUTPUTDIR: /var/www/tests
URL: http://repo-genesis3.cbi.utsa.edu/tests
DOXYGENOUTPUT: /var/www/doxygen
DOXYGENURL: http://repo-genesis3.cbi.utsa.edu/doxygen
REGTESTOUTPUT: /var/www/regtests
REGTESTURL: http://repo-genesis3.cbi.utsa.edu/regtests
LOGROTATE: 10

```

---
# Cron Job #

There are several ways to run a cronjob on a machine, here are some of the easiest ways to do this on a Linux based machine. These typically require administrator rights to set up.

**cron.d**
This is a directory in /etc/, you can put a cronjob formatted text file in here and it will get picked up by the cron daemon. A typical invocation for neurospaces\_cron would be with a line like this:

01 00 `*` `*` `*` g3tester neurospaces\_cron

This particular job is set to run at exactly one minute after midnight. If you want to have the job run more frequently you can do this:


01 `*`/8 `*` `*` `*` g3tester neurospaces\_cron

This makes the job run every 8 hours.

g3tester is a  user set up for permissions to perform a 'sudo make install,' and 'sudo make uninstall.' To set up a regular user to use sudo, add them to the /etc/sudoers file (see next section).

---
# Sudoers File #

The suoders file is a file which grants a particular user the ability to run a command or set of commands in super user mode. It is found in /etc/sudoers. There are some variables here which must be set in order to allow a regular user to perform a neurospaces\_cron job since it involves doing an install and uninstall of the neurospaces\_project to the system.

To allow out g3tester user to perform any command anywhere on the system in super user mode you would enter in a line like this:

_g3tester    ALL=(ALL)       ALL_

This will however still ask for a password, which will stop a cronjob. To avoid the system asking for a password we use the NOPASSWD option like so:

_g3tester        ALL=(ALL)       NOPASSWD: ALL_

Since it is desirable to only allow a regular user to perform commands they absolutely need to run we can constrain their privileges my inserting a command list:

_g3tester        ALL=(ALL)       NOPASSWD: /usr/bin/make /usr/local/bin/neurospaces\_cron_

This now gives g3tester the ability to execute only the commands neurospaces\_cron and make, without needing a password. More commands can be added as needed simply by appending them to the list with a space delimiter.