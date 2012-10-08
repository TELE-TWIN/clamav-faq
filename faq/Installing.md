# Installing ClamAV #

* [Installing from source][]
* [Installing from packages][]

## Installing from source ##

* Check [Requirements][]
* Uninstall any old version: see UninstallClamAV (Note: this isn't essential, but removes sources of problems).
* wget the source gzip file: see WhichVersion
* untar the source to an appropriate location. Note that most modern versions of tar will automatically ungzip for you.
* ensure you have a user clamav and group clamav.
>* If you're using a different group/user you must specify that using the --with-user and/or --with-group option when you run configure
* Configure the build:
>* for clamav-milter support ./configure --enable-milter
>* to support new features ./configure --enable-experimental
>* otherwise ./configure
* run "make"
* if you did not uninstall the previous version, stop any running services.
* as root, run "make install" to install the binaries
* it is recommended that you run ldconfig as root after installing
* restart any relevant services.
* Run freshclam manually, to ensure your AV definitions are up to date.

### Requirements ###

* C compiler
* zlib library
>__Note:__ you must compile with a shared library, use:
>_make clean ; ./configure -s_ __before__ _make_ when building zlib

Optional
: GMP: for digital signatures
: cURL: for mail follow url


## Installing From Packages ##

Installing your distribution's packages is the easiest route. It will make also upgrades easier.

### Understand which packages you need ###

You don't necessarily need all packages. Please read ClamOverview carefully to understand which ones you need.

## Operating System Specific Information ##

* [Debian][]
* [RedHat and Fedora][]
* [Mandriva][]
* [Gentoo][]
* [SuSE][]
* [FreeBSD, OpenBSD, NetBSD][]
* [Solaris][]
* [Slackware][]
* [Windows][]
* [CentOS][]
* [OpenVMS][]

### Debian ###

* apt-get update
* apt-get install clamav

#### Available packages ####

* _clamav-getfiles_ - Update script for clamav
* _clamav_ - antivirus scanner for Unix
* _clamav-base_ - base package for clamav, an anti-virus utility for Unix
* _clamav-daemon_ - antivirus scanner daemon
* _clamav-data_ - clamav data files
* _clamav-docs_ - documentation package for clamav, an anti-virus utility for Unix
* _clamav-freshclam_ - downloads clamav virus databases from the Internet
* _clamav-milter_ - antivirus scanner for sendmail
* _clamav-testfiles_ - use these files to test that your Antivirus program works
* _clamav-dbg_ - debug symbols for clamav

### RedHat and Fedora ###

#### Packages for Fedora 9 ####

http://matrix.senecac.on.ca/~mpaivaneto/clamav-0.94-1.fc9.i386.rpm
http://matrix.senecac.on.ca/~mpaivaneto/clamav-0.94-1.fc9.src.rpm

* rpm -ivh http://matrix.senecac.on.ca/~mpaivaneto/clamav-0.94-1.fc9.i386.rpm

For more information contact me at mpaivaneto@learnDEL_ME.senecac.on.ca or at [my blog]

Packages that you'll need to download:

* clamav-db-0.xx.rpm
* clamav-devel-0.xx.rpm
* clamav-0.xx.rpm
* clamd-0.xx.rpm

Fresh Installation

* rpm -Uvh clamav-db-0.xx.rpm
* rpm -Uvh clamav-devel-0.xx.rpm
* rpm -Uvh clamav-0.xx.rpm
* rpm -Uvh clamd-0.xx.rpm

Upgrading clamav engine from rpms

* rpm -Uvh clamav-db-0.xx.rpm clamav-devel-0.xx.rpm clamav-0.xx.rpm clamd-0.xx.rpm

__Note:__ *Richard Vinke* reports The file "clamd-0.xx.rpm" is not available for fedora5. I am using "rpm -Uvh" without this file. I needed the following post-install actions:

* Change the ownership of the directory _/var/lib/clamav_ to the user as stated in the configuration file.
* Rename the _/etc/clamd.conf.rpmsave_ to _/etc/clamd.conf_.

You must upgrade the rpms at the same time, otherwise it will result in dependencies of the previous installation

#### Updates Particular to Fedora 6 ####

As Richard Mentions, "The file "clamd-0.xx.rpm" is not available..." you should grab _clamav-server-0.XX.rpm_ and install that in its place.

Sources:

>[RPM Packages for Fedora/Redhat]
>
>[RPM Packages for CentOS]

### Mandriva ###

* urpmi clamav

### Gentoo ###

Nobody uses Gentoo.

### SuSE ###

For newbies, the easiest way to install ClamAV is to find an RPM for SuSE. Look in ftp://ftp.suse.com/pub/projects/clamav/. Go one level down to see if there is a newer version. On the next level choose your SuSE Version and Processor-Type e.g., 9.3-i386 for SuSE 9.3 on a i386 compatible system. The program file requires about 1 MB, and the database file requires about 8 MB. The program file is the most important. When you run _freshclam_ later, it may update the database without needing to do this database ftp. But if _freshclam_ fails, come back and get this database RPM and install it like the other.

If you use the generic source/compile download (not SuSE RPM) it will, by default, put the programs and documentation in places that SuSE does not expect. Then it is hard to determine which files you're really running from, especially for a newbie to SuSE.

* Use ftp or wget to download the rpm file to a folder on your PC.
* Click on the icon representing the file. This should start a window with some information tabs. Review them if you wish.
* Click on the "Install with YaST" button.
* The installer may demand the SuSE installation DVD, or installation CD #4 (for older SuSE installations). Go ahead and humor the installer. It will pretend to install the older version, but the new one will actually be installed.
* Open a _konsole_ or _gnome-terminal_ terminal/shell window.
* Run _/bin/su_ and then enter the root password. (I always do this out of habit. I get fewer gripes from the system that way.)
* Enter _freshclam_. (It should update the database from the latest version. Be sure the modem/LAN is on and internet connected. If this doesn't work, go back to #1 and use ftp to download the database.)
* Change directory to "/" by entering _cd /_.
* Enter _clamscan -i -r --detect-broken_ (This will scan every file on the system. It may take a while if you've got a lot of files. It will report only the problems found.)
* For more information, enter _man clamscan_ or _man freshclam_. Other interesting files are _/etc/clamd.conf_ and _/etc/freshclam.conf_. You can run _find top_dir -name "clam*" -ls_ to find other files that may be of interest.

Another way to install the downloaded file is via rpm.

* _rpm -Uvh clamav-version-0.1.platform.rpm_ to upgrade (update) the installation.

### FreeBSD, OpenBSD, NetBSD ###

Use the ports Luke.

### Solaris ###

There are a number of package maintainers for ClamAV on Solaris. The installation method differs for each.

#### Solaris 9 & Solaris 10 Packages from Citrus IT ####

Download the package file from [clamav.citrus-it.net].

It will be a single bzip2-compressed package datastream file with a filename of the form ClamAV-<version>.bz2 or ClamAV-s10-<version>.bz2

Uncompress the file

>  # bzip2 -d ClamAV-s10-<version>.bz2

Install the package (the example output is for the Solaris 10 package)

>  # pkgadd -d ClamAV-s10-<version>
<pre>
The following packages are available:
  1  ClamAV     ClamAV 0.92
                (sparc) 0.92

Select package(s) you wish to process (or 'all' to process
all packages). (default: all) [?,??,q]: 1

Processing package instance <ClamAV> from </tmp/ClamAV-s10-0.92>

ClamAV 0.92(sparc) 0.92
Citrus IT Limited


**********************************************************************

Would you like to download the latest virus pattern definitions during
installation ?  (This requires that you have a direct connection to the
Internet. If you are behind a proxy server then skip this step.)

**********************************************************************


Update virus patterns? (y/n): y

 ## Processing package information.
 ## Processing system information.
 ## Verifying package dependencies.
 ## Verifying disk space requirements.
 ## Checking for conflicts with packages already installed.
 ## Checking for setuid/setgid programs.

This package contains scripts which will be executed with super-user
permission during the process of installing this package.

Do you want to continue with the installation of <ClamAV> [y,n,?] y

Installing ClamAV 0.92 as <ClamAV>

 ## Executing preinstall script.
Creating group clamav...
Creating user clamav...
 ## Installing part 1 of 1.
/lib/svc/method/clamav
/opt/ClamAV/bin/clamav-config
/opt/ClamAV/bin/clamconf
/opt/ClamAV/bin/clamdscan
/opt/ClamAV/bin/clamscan
/opt/ClamAV/bin/freshclam
/opt/ClamAV/bin/sigtool
/opt/ClamAV/etc/clamd.conf
/opt/ClamAV/etc/freshclam.conf
/opt/ClamAV/etc/freshclam.conf.install
/opt/ClamAV/include/clamav.h
/opt/ClamAV/lib/libclamav.a
/opt/ClamAV/lib/libclamav.la
/opt/ClamAV/lib/libclamav.so <symbolic link>
/opt/ClamAV/lib/libclamav.so.3 <symbolic link>
/opt/ClamAV/lib/libclamav.so.3.0.3
/opt/ClamAV/lib/libclamunrar.a
/opt/ClamAV/lib/libclamunrar.la
/opt/ClamAV/lib/libclamunrar.so <symbolic link>
/opt/ClamAV/lib/libclamunrar.so.3 <symbolic link>
/opt/ClamAV/lib/libclamunrar.so.3.0.3
/opt/ClamAV/lib/libclamunrar_iface.a
/opt/ClamAV/lib/libclamunrar_iface.la
/opt/ClamAV/lib/libclamunrar_iface.so <symbolic link>
/opt/ClamAV/lib/libclamunrar_iface.so.3 <symbolic link>
/opt/ClamAV/lib/libclamunrar_iface.so.3.0.3
/opt/ClamAV/lib/pkgconfig/libclamav.pc
/opt/ClamAV/sbin/clamav-milter
/opt/ClamAV/sbin/clamd
/opt/ClamAV/share/clamav/daily.inc/COPYING
/opt/ClamAV/share/clamav/daily.inc/daily.cfg
/opt/ClamAV/share/clamav/daily.inc/daily.db
/opt/ClamAV/share/clamav/daily.inc/daily.fp
/opt/ClamAV/share/clamav/daily.inc/daily.hdb
/opt/ClamAV/share/clamav/daily.inc/daily.hdu
/opt/ClamAV/share/clamav/daily.inc/daily.info
/opt/ClamAV/share/clamav/daily.inc/daily.mdb
/opt/ClamAV/share/clamav/daily.inc/daily.mdu
/opt/ClamAV/share/clamav/daily.inc/daily.ndb
/opt/ClamAV/share/clamav/daily.inc/daily.ndu
/opt/ClamAV/share/clamav/daily.inc/daily.pdb
/opt/ClamAV/share/clamav/daily.inc/daily.wdb
/opt/ClamAV/share/clamav/daily.inc/daily.zmd
/opt/ClamAV/share/clamav/main.cvd
/opt/ClamAV/share/clamav/mirrors.dat
/opt/ClamAV/share/man/man1/clamconf.1
/opt/ClamAV/share/man/man1/clamdscan.1
/opt/ClamAV/share/man/man1/clamscan.1
/opt/ClamAV/share/man/man1/freshclam.1
/opt/ClamAV/share/man/man1/sigtool.1
/opt/ClamAV/share/man/man5/clamd.conf.5
/opt/ClamAV/share/man/man5/freshclam.conf.5
/opt/ClamAV/share/man/man8/clamav-milter.8
/opt/ClamAV/share/man/man8/clamd.8
/var/svc/manifest/network/clamav.xml

[ verifying class <none> ]

 ## Executing postinstall script.

svccfg: Taking "initial" snapshot for svc:/network/clamav:default.
svccfg: Taking "last-import" snapshot for svc:/network/clamav:default.
svccfg: Refreshed svc:/network/clamav:default.
svccfg: Refreshed svc:/milestone/multi-user:default.
svccfg: Successful import.

 *** Downloading latest virus pattern definitions.

Current working dir is /opt/ClamAV/share/clamav
Max retries == 3
ClamAV update process started at Sun Dec 30 20:03:53 2007
Querying current.cvd.clamav.net
TTL: 300
Software version from DNS: 0.92
main.cvd version from DNS: 45
main.cvd is up to date (version: 45, sigs: 169676, f-level: 21, builder: sven)
daily.cvd version from DNS: 5297
Retrieving http://database.clamav.net/daily.cvd
Trying to download http://database.clamav.net/daily.cvd (IP: 193.19.98.136)
Downloading daily.cvd [100%]
Removing incremental directory daily.inc
Removing backup directory ./clamav-a1b9834acd3a8a1360172186199f917d
daily.inc updated (version: 5297, sigs: 14304, f-level: 21, builder: ccordes)
Database updated (183980 signatures) from database.clamav.net (IP: 193.19.98.136)

**********************************************************************

Installation is complete.

You should now review the following configuration files and customise
them for your environment.

 /opt/ClamAV/etc/clamd.conf
 /opt/ClamAV/etc/freshclam.conf

and then the daemons can be started with:

 svcadm enable clamav

For assistance with this package, please email clamav@citrus-it.net
in the first instance. For general assistance with ClamAV, see
http://www.clamav.net/ & http://wiki.clamav.net/

**********************************************************************
Installation of <ClamAV> was successful.
</pre>

Verify that the ClamAV service has been installed into the SMF database (Solaris 10 only)

 # svcs clamav
STATE          STIME    FMRI
disabled       20:06:24 svc:/network/clamav:default
Test the command line scanner

 # /opt/ClamAV/bin/clamscan /tmp/ClamAV-s10-0.92 
/tmp/ClamAV-s10-0.92: OK

----------- SCAN SUMMARY -----------
Known viruses: 183980
Engine version: 0.92
Scanned directories: 0
Scanned files: 1
Infected files: 0
Data scanned: 19.48 MB
Time: 13.060 sec (0 m 13 s)
Configure the daemon scanner and updater

Edit the two configuration files in _/opt/ClamAV/etc_ to suit your installation then start the ClamAV services.

>  # svcadm enable clamav

Instructions courtesy of Andy Fiddaman - 30 Dec 2007

#### Solaris packages from [OpenCSW] ####

OpenCSW is a community software project for Solaris 8+ on both Sparc and x86. It packages more than 2000 popular open source titles and they can all easily be installed with dependency handling via _pkgutil_ which is modeled after Debians _apt-get_.

>  # pkgutil -i clamav

More info on [OpenCSW]


### Slackware ###

Linuxpackages.net provides third-party precompiled packages for Slackware. You can find them with this search query on that site.

Martijn Dekker provides packages there that provide complete and semi-automatic integration with ClamAV's stock Sendmail package.

#### How to use ####

Download the package, and as root, install it like so (substituting the appropriate filename):

>  # installpkg clamav-0.91.2-i486-1McD.tgz

To activate Sendmail integration, after installing the package, copy the ‘/usr/share/sendmail/sendmail-slackware-clamav.cf’ file into ‘/etc/mail/sendmail.cf’:

>  # cp /usr/share/sendmail/sendmail-slackware-clamav.cf /etc/mail/sendmail.cf

Then start ClamAV and restart Sendmail:

>  # /etc/rc.d/rc.clamav start 
>  # /etc/rc.d/rc.sendmail restart

#### Building your own ####

You may wish to build your own package if I haven't uploaded one with the most recent version yet, if you use a pre-11.0 Slackware version (at the time of this writing, I build on 11.0, 12.0 and 12.1, and my binaries may not work on earlier versions), if you don't trust third-party binaries, or simply because you're a complete geek ;-) . You can download my easy-to-use Slackbuild script, from which the fully-integrated ClamAV packages at Linuxpackages.net are generated.

This script can be used to build a ClamAV package for Slackware 10.0 or higher with Sendmail installed (as Sendmail milter support was introduced as of 10.0). To choose a version of ClamAV to build, you can ‘cd’ to the script's directory and invoke the script like so:

> $ VERSION=1.23.4 ./clamav.SlackBuild

…substituting, of course, the appropriate ClamAV version for ‘1.23.4’. Note: there is no need to be root to use this build script; it will ask for your root password after building the binaries and just before creating the package (and if you have fakeroot installed, even that isn't necessary).

### Windows ###

#### First update Windows ####

* Microsoft Update

#### Available packages ####

clamAV.msi - base package for clamav, an anti-virus utility for Windows

#### How to install ####

* simple mode: doubleclick the MSI installer package
* command line (displays only a confirmation dialog at the end): msiexec /i clamAV.msi /qr

#### Requirements ####

Microsoft .net version 2.0 is required starting with ClamAV 0.92.1

### CentOS ###

To install on CentOS using yum

* create the file: _/etc/yum.repos.d/dag.repo_

>[dag]
>
>name=Dag RPM Repository for Red Hat Enterprise Linux
>
>baseurl=http://apt.sw.be/redhat/el$releasever/en/$basearch/dag/
>
>gpgcheck=1
>
>gpgkey=http://dag.wieers.com/packages/RPM-GPG-KEY.dag.txt
>
>enabled=1

* Then do: _# yum install clamd.i386_

* Restart the clamd service: _# /etc/rc.d/init.d/clamd restart_

Instructions courtesy of Kieran Egan - 07 Apr 2008

I just did: _# yum update clamd_ ...and it all went wrong.

The problem was it installed the new binaries in a different location, so now I had two versions (see http://www.clamav.org/support/faq/)

Doing: _# whereis clamscan_ and _# whereis freshclam_ showed the problem. The solution was the following set of commands run as root. Cut and paste into a shell script or type in manually.

* rename the old binaries

>mv /usr/local/bin/clamscan /usr/local/bin/clamscan.old
>
>mv /usr/local/bin/freshclam /usr/local/bin/freshclam.old
>
>mv /usr/local/bin/clamdscan /usr/local/bin/clamdscan.old

* Create links to the new ones in /usr/local/bin

>ln -s /usr/bin/clamscan /usr/local/bin/clamscan
>
>ln -s /usr/bin/freshclam /usr/local/bin/freshclam
>
>ln -s /usr/bin/clamdscan /usr/local/bin/clamdscan

* There was a fourth new clam binary - _/usr/bin/clamconf_

>ln -s /usr/bin/clamconf /usr/local/bin/clamconf

* Update virus database and go

>freshclam

Instructions courtesy of  Kieran Egan - 09 May 2008

### OpenVMS ###

The ClamAV [for OpenVMS] port is mantained by Alexey Chupahin, Mibok Ltd

Please visit Clamav [OpenVMS project site]

First, you should download latest clamav sources and bzip2 library (if you need bz2 archives support) from the site above. Install process is very similar to one in unix:

>@configure
>
>@build
>
>@clamav$startup

This process provides for you:

* ClamAV library
* clamscan
* freshclam
* clamd
* clamdscan 
* clamconf 
* scripts, allow you to start clamd and freshclam in daemon mode

-- AlexeyChupahin - 13 Dec 2008


[my blog]: http://miltonpaiva.wordpress.com/
[RPM Packages for Fedora/Redhat]: http://crash.fce.vutbr.cz/crash-hat/4/clamav/
[RPM Packages for CentOS]: http://crash.fce.vutbr.cz/crash-hat/centos/5/clamav/
[OpenCSW]: http://www.opencsw.org
[OpenVMS project site]: http://clamav.dyndns.org/clamav
[for OpenVMS]: http://www.openvms.org/
[clamav.citrus-it.net]: http://clamav.citrus-it.net/
