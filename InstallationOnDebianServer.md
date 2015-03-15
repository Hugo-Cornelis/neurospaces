G3 Doc: installation-debian-server

# Introduction #

This is a guide for setting up GENESIS 3 on a new Debain 5.0.3 installation, with the tester, code repository and webserver running.


# Setting up the Web Server #

Test run output as well as generated html files from programs like Xrefactory and Doxygen need to be accessible over the internet. This is a guide on adding the apache2 rules to set up a directory that will have its contents displayed via the http server.

## DocumentRoot ##

The DocumentRoot is the publicly available directory displayed when a user visits the bare URL for your server with a web browser (example: http://myserver.edu). If you want data to be available over the web, you can simply place it in the document root. If you place a file named _'mydata'_ in the DocumentRoot, it will be available at the URL http://myserver.edu/mydata.

In case you want directories in another location on the machine to be displayed via the URL with a suffix see the next section.


## Configuration File /etc/apache2/apache2.conf ##

The default http configuration file for the Apache Web Server in Debian is in the file _/etc/apache2/apache2.conf_. The cleanest method for doing this is to put all of your rules in a separate file, then add an **Include** statement to the default configuration file to include your file. As in this example:

```
Include /etc/apache2/my.conf
```


To start your configuration file you'll need a VirtualHost block. So the initial conf file should look like this.

```
NameVirtualHost *:80

<VirtualHost *:80>

</VirtualHost>
```


In your configuration file you will explicitly declare which directories you want served within the VirtualHost block. If you want to serve the directory _/var/www/tests_ for test output you simply declare a block in the your configuration file for the directory and an Alias directive so that the directory can be accesible via a suffix to the URL (such as http://myserver.edu/tests). Your configuration file should end up looking like this:

```
NameVirtualHost *:80

<VirtualHost *:80>

   <Directory /var/www/tests>
     Options +FollowSymLinks Indexes MultiViews
     AllowOverride All
     order allow,deny
     allow from all
   </Directory>

   Alias /tests /var/www/tests/

</VirtualHost>
```

For each directory you'd like to serve you must add a block and an alias.
After you updated your configuration restart apache with this command:

**apache2ctl restart**

You should now be able to view the contents of the directory /var/www/tests from your URL suffixed with your alias.


---


# Installing the Developer Package #

## Dependencies ##


### Install gcc ###

Install gcc by executing the following command as root:

**apt-get install gcc**


### Install autotools ###

Debian by default usually doesn't have development tools installed. You will need to use apt-get to install automake, make with the following commands:

**apt-get install automake**
**apt-get install make**

### Perl Dependencies ###

Install the following Perl modules from CPAN:

  * YAML
  * IO::Pty
  * Expect
  * Data::Comparator
  * File::Find::Rule
  * Inline

_note: you must install gcc first in order to compile Perl modules from CPAN._

## Additional Dependencies ##

These additional dependencies must be installed via apt-get:

  * psmisc (for the killall command)
  * daemon (to run neurospaces\_serve as a daemon process)
  * sudo
  * libncurses5-dev
  * flex
  * bison
  * swig
  * libperl-dev
  * libreadline5-dev
  * python-dev


---


# Setting up the Monotone Repository Server #

The GENESIS project uses monotone for its source code versioning system. The Developer Package contains scripts for starting up the monotone repositories, but first some things must be set up for it to run correctly.

## Install Monotone ##

Install monotone, it is freely available from http://monotone.ca/.

Simply download the static binary and place it in your path.

## Creating a 'monotone' user ##

It is bad practice to run a monotone repository as root so the first thing that should be done is create a non-privileged user for monotone to run as. On the root shell prompt enter:

**useradd monotone -b /home/monotone**

_note: if the user directory is not created, you must create it in the /home directory via the mkdir command: mkdir /home/monotone. Then you must perform a chown to set the permissions:_chown -R monotone:monotone /home/monotone.


## Setting up permissions ##

### Setting up a server key ###

As the monotone user execute the command:

**mtn genkey email@address.com**

with an email address that the server can be identified with. Enter in a passphrase for it.

The key will then be stored in the directory ~/.monotone/ and will be automatically used when the monotone user invokes any monotone command. Now to prevent monotone from asking for this passphrase every time it performs an action create a file within ~/.monotone with the name _monotonerc_. Within this file enter this lua function:

```
function get_passphrase(keypair_id)
       return "my passphrase"

```

this will give your passphrase to monotone automatically, preventing the process from stopping to ask for user input. If the monotone user directory is readable across the system it is advisable to not to use a password shared on the machine that other users may be able to read from the monotonerc file.

### Read Permissions ###

To set who can read from the monotone repositories you serve create a file in ~/.monotone called _read\_permissions_. Inside this file you can set patterns, to allow everyone to read all repositories enter in:

```
 pattern "*"
     allow "*"
```

### Write Permissions ###

To set who can write to a repository you must create a file within the ~/.monotone directory called _write\_permissions_. Here you simply type each email address, the identifier used when setting up your key, one line at a time.

In addition to this you must read in a public key on each monotone repository. For instructions on how to do this see the _Repository Access_ section of the developer-repository document.


## Starting the montone repository servers ##

First you must have the compliant directory setup for the developer packages scripts to find the repositories to serve:

**~/neurospaces\_project/MTN**

Inside the MTN directory you have repositories for each of the projects you will be serving over the web:

```
developer.mtn		model-container.mtn	studio.mtn
gshell.mtn		ns-sli.mtn		userdocs.mtn
heccer.mtn		ssp.mtn
```

You can then issue the command:

**nohup neurospaces\_serve &**

as your monotone user or you can issue the command:

**sudo -H -u monotone nohup neurospaces\_serve &**

as root to start the repository servers. They can now be connected to over the internet for pulls, pushes, and syncs.


---


# Setting up the Tester #

The neurospaces tester is a script which performs a routine check of the GENESIS 3 repository, source code, and documentation.  It is meant to be set up as a cronjob on a server with email capabilities, so that notification of a test run can be emailed to designated recipients.

When the script is set up, it ensures that errors with the system are reported in a timely fashion.


## Sending Mail ##

By default Debian is configured for local mail only. To send email over the internet you will need to set the **dc\_eximconfig\_configtype** option in the file **/etc/exim4/update-exim4.conf.conf** from 'local' to 'internet'.

Then perform a restart of exim via the command:

_/etc/init.d/exim4 restart_


---


# Building userdocs #

## Dependencies ##

To build the user documentation you need to install **latex** and all of its dependencies via apt-get:

  * tetex-base
  * tetex-bin
  * tetex-doc (Might not need to install this as it may be included with tetex-base)
  * tetex-extra
  * tex4ht
  * webcheck (For link checking)

For level 6 documentation you need to install:

  * doxygen
  * graphviz (If you want graphs in your doxygen output)

For building regression test output you need:

  * source-highlight

The following CPAN modules for Perl must also be installed:

  * HTML::Table
  * HTML::Template
