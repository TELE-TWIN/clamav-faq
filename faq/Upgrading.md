Title:	Upgrading
Author:	Sourcefire VRT
Affiliation:	Sourcefire VRT
Date:	2012-06-26
Subject:	ClamAV - Upgrading
Base Header Level:	2
Copyright:	Sourcefire Inc.
CSS:	/style/clam.css

# Upgrading ClamAV #

* [If you installed from packages][]
* [If you installed from sources][]
* [Upgrade notes][]
* [Webmin and yum][]

## If you installed from packages ##

Find new packages and install them. See ClamPackages

If there are no new packages, you have three options:

* Wait
* BuildClamPackage
* InstallFromSource

## If you installed from sources ##

Uninstall the old version

* ./configure
* sudo make uninstall

Compile and install the new one: see [Installing ClamAV]

__Note:__ Depending on your installation method, you might want to backup configuration (located in _/usr/local/etc_ by default) and signature database (located in _/usr/local/share/clamav_ by default). Don't forget to restore backups before starting up updated ClamAV.

__Note:__ Backup your database signature (located in _/usr/local/share/clamav_ by default) before upgrading to newer ClamAV version. Restore the backed up database signature before running the updated version. This is to avoid getting the _/usr/local/share/clamav not locked_ error message when doing _freshclam_.

## Upgrade notes ##

UpgradeNotes097
UpgradeNotes0963
UpgradeNotes0962
UpgradeNotes0961
UpgradeNotes096
UpgradeNotes0953
UpgradeNotes095
UpgradeNotes094
UpgradeNotes093
UpgradeNotes091
UpgradeNotes0901
UpgradeNotes090

## Webmin and yum ##

If you obtained a server from a competent hosting provider they probably already installed clamav using yum and the extras repository. To obtain a new version:

>yum list clamav

>yum update clamav (if everything updated properly, run freshclam)

[Installing ClamAV]: /Installing.html