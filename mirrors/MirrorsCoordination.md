# Official mirror howto

## Bandwidth limit

You are encouraged to put some bandwidth limit on your ClamAV mirror vhost.

Many mirror sysadmins running the [Apache HTTP Server](http//httpd.apache.org/) find the [Bandwidth Mod](http://bwmod.sourceforge.net/) useful for this purpose. 

Here is an example config that will do the following:

  * Limit .cvd downloads to 40KB/s
  * Limit .cdiff downloads to 400KB/s
  * Allow max 50 simultaneous connections
  * Minimum download speed 20KB/s

### Apache ###

<pre><IfModule mod_bw>
  BandWidthModule On
  ForceBandWidthModule On
  LargeFileLimit .cvd 1 40000
  LargeFileLimit .cdiff 1 400000
  MaxConnection all 50
  MinBandwidth all 20000
</IfModule>
</pre>


### Lighttpd ###

<pre>$HTTP["url"] =~ "\.cvd$" {
  server.max-connections = 50
  connection.kbytes-per-second = 40
}
$HTTP["url"] =~ "\.cdiff$" {
  server.max-connections = 50
  connection.kbytes-per-second = 400
}
</pre>


### Nginx ###

<pre>if ( $request_uri ~ "\.cvd$" ) {
       set $limit_rate 40k;
}
if ( $request_uri ~ "\.cdiff$" ) {
       set $limit_rate 400k;
}
</pre>

_Note_ You can also use mod_cband to limit the download-speed.

The Source is available at http://cband.linux.pl/download/ or http://sourceforge.net/projects/cband/

Just run
<pre>./configure
make
make install
</pre>

Add the module to your apache-config by manually adding it to _/etc/apache2/httpd.conf_

<pre>LoadModule cband_module  /usr/lib/apache2/modules/mod_cband.so
</pre>

Edit the config for the vhost and add

<pre><IfModule mod_cband.c>
  CBandSpeed 20kb/s 100 300
  CBandRemoteSpeed 20kb/s 100 300
</IfModule></pre>

This limits the download speed to 20kb/s, allows 100 requests/second and a maximum of 300 connections.

To improve mod_cband's performance add.

<pre>CBandScoreFlushPeriod 1
CBandRandomPulse On</pre>

to _/etc/httpd/conf/httpd.conf_

Done. Now restart apache.

If you would like a scoreboard, set something like.

<pre><IfModule mod_cband.c>
  CBandSpeed 20kb/s 100 300
  CBandRemoteSpeed 20kb/s 100 300
  <Location /cband-status>
    SetHandler cband-status
  </Location>
</IfModule></pre>

Create the directory for CBandScoreboard and make it writeable by the apache-user.

<pre>$ chown wwwrun.www /srv/www/scoreboard/</pre>

The status page can be found on http://your-domain/cband-status.

With mod_cband you can also limit the download speed based on monthly traffic or the source-ip. For more information see http://codee.pl/cband_documentation.html

Thanks to Florian Schaal, 2011-07-14

# Reducing traffic #

## The centrally maintained blacklist ##

We provide a configuration file for Apache and Lighttpd which lists.

  * IP addresses that are reported by our mirrors as abusers
  * obsolete ClamAV installations

The names of the files are.

  * local_blacklist_apache - written following Apache mod_access syntax, it can be used as an .htaccess file
  * local_blacklist_lighttpd - meant to be included into lighttpd config.

Our script (clam-clientsync) by default excludes all files with a local_* prefix, so you won't see this file on your mirror.

You can write your own script to regularly download this file and use it on your webserver.

If you are running Apache and mod_access is enabled (most common setup), this can be as simple as this.

<pre>#!/bin/bash
. $HOME/etc/clam-clientsync.conf
export RSYNC_PASSWORD
rsync $RSYNC_USER@rsync.clamav.net::$MODULE/local_blacklist_apache $TO/.htaccess
</pre>

### Lighttpd ###

<pre>#!/bin/bash
. $HOME/etc/clam-clientsync.conf
export RSYNC_PASSWORD
rsync $RSYNC_USER@rsync.clamav.net::$MODULE/local_blacklist_lighttpd /path/local_blacklist_lighttpd</pre>

You will need to include local_blacklist_lighttpd into main.conf, like this.

<pre>$HTTP["host"] =~ "^(clamav\.yourhostname\.tld|.*\.clamav\.net)$" {                     
    include "/path/local_blacklist_lighttpd"
</pre>


## Additional tweaks ##


### Manually blackList old versions of ClamAV ###

We kindly ask our mirrors to support as many old versions of ClamAV as possible. However we understand that this can eat a lot of resources and not every mirror can afford it. Hereby we provide some config. examples for various web servers.


### Apache HTTP Server ###

<pre>SetEnvIfNoCase User-Agent "^clamav/0.6" bad_clamav 
SetEnvIfNoCase User-Agent "^clamav/devel-2008" bad_clamav
SetEnvIfNoCase User-Agent "^ClamWin/0.6" bad_clamav
</pre>

<pre><Location "/">
  Order allow,deny
  Allow from all
  Deny from env=bad_clamav
</Location>
</pre>


### Lighttpd ###

<pre>$HTTP["useragent"] =~ "^clam(av|Win)\/(0.[67]|.*devel).*$" {
  url.access-deny = ( "" )
}</pre>


### Nginx ###

<pre>if ( $http_user_agent ~* "^clam(av|win)\/(0\.[67]|devel-200[0-8]|devel-0\.[0-8]).*$" ) {
  return 404;
}</pre>


## Block outdated clients

Due to numerous connects of outdated clients (&gt; 300,000 / day), we add single IP temporarily to the firewall.

Requirements:

   * [Apache HTTP-Server](http://httpd.apache.org/)
   * [syslog-ng](http://www.balabit.com/)
   * [iptables](http://netfilter.org/projects/iptables/index.html)
   * Kernel with recent-support

## Configure Apache HTTP-Server

[[MirrorsCoordination#Additional_tweaks_manually_black][Setup manually blackList old versions of ClamAV]]

The Access Log of apache must send to syslog-ng:

<pre>$ mknod /var/log/apache/access.log p</pre>

### Apache HTTP Server (httpd.conf or vhost.conf) ###

The logformat and the accesslog must be defined like:

<pre>LogFormat "%h %v %l %u %t \"%r\" %&gt;s \"%{Referer}i\" \"%{User-Agent}i\"" syslog<br />CustomLog= =/var/log/apache2/access.log syslog</pre>

As long as the log file runs only through the pipe, no entries are stored. The configuration used here evaluates merely the log file. To receive an Access Log as a file, you must extend either syslog-ng by a destination or the apache-config by a CustomLog.

## Configure syslog-ng ##

<pre>source s_apache_access { <br /> pipe("/var/log/apache2/access.log"); <br /> };<br /><br />destination d_clamav-403 { <br /> file("/proc/net/xt_recent/clamav-403"<br /> template("+${APACHE.SRC-IP}\n")); <br /> };<br /><br />filter f_clamav_403 { <br /> message('clamav.net') <br /> and message(' 403 '); <br /> };<br /><br />parser p_apache_src_ip { <br /> csv-parser(columns("APACHE.SRC-IP")<br /> delimiters(': ') <br /> flags(escape-none,greedy) <br /> template("${MSGHDR}") ); <br /> };<br /><br />log { <br /> source(s_apache_access);<br /> filter(f_clamav_403);<br /> parser(p_apache_src_ip);<br /> destination(d_clamav-403); <br /> };</pre>

## Iptables

<pre>iptables -A INPUT -p tcp --dport 80 -m recent --rcheck --name clamav-403 --seconds 3600 --hitcount 5 -j DROP</pre>

## how it works

Syslog-ng filters apache messages with the contents clamav.net and 403. As destination /proc/net/xt_recent/clamav-403 is defined. The template adds the IP to the firewall. With reach from "hitcount" the IP is blocked "seconds".

If you replace the _rcheck_ here with an _update_ statement, the block will last even longer. The _rcheck_ option means: we will block you for the next hour. While _update_ means: we don't want to see you for an hour, but if we see you again during this time, we'll block you again. It means that you actually need to be quiet for 60 minutes to be able to log in again.

By default xt_recent stores 100 IP addresses. You can change the limit with "modprobe ipt_recent ip_list_tot=10000" (here 10000). This is only possible before the first iptables rule is put on.

Use:
<pre>$ chmod 600 /sys/module/xt_recent/parameters/ip_list_tot<br /> echo 10000 &gt; /sys/module/xt_recent/parameters/ip_list_tot<br /> chmod 400 /sys/module/xt_recent/parameters/ip_list_tot</pre>

to change ip_list_tot "on-the-fly"

If you use ipt_recent instead of xt_recent, be sure to modify the filenames and path.

Credits: [Valentijn Sessink](http://valentijn.sessink.nl/?p=322)


