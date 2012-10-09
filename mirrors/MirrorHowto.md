# Introduction

## This doc

The latest version of this document is always available at [MirrorHowTo](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/MirrorHowto.md). Before going any further, please check that you are reading the latest version.

Japanese sysadmins can find a translated version of this doc at http://www.orange.co.jp/~masaki/clamav/mirror-howto-jp.html (not necessarily up to date)

## The need for mirrors

To prevent the spread of worms it is essential to check for updates frequently. ClamAV users often configure freshclam with a check interval of 30 minutes. With an exponentially growing number of ClamAV users, the servers hosting the virus database files get easily overloaded. For this reason we must continuously add more mirrors to our pool.

# Becoming a Mirror #


## Requirements

We need fast reliable mirrors. Servers eligible to become mirrors have to meet the following requirements:

   * At least a 10Mbit/s link to the Internet. Traffic is bursty, that's why we request such a large pipe
   * Unlimited traffic (between 500GB and 750GB/month as of 2010)
   * At least 150MB of web space
   * sshd listening on port 22 (see [Update Firewall][] for an alternative solution)
   * All the tools and protocols required for our push-mirroring system: rsync, ssh, bash, lockfile. See below for the details.
   * The mirror has to be available to all ClamAV users. We DO NOT support private mirrors!
We also appreciate (but do not require) having shell access to the server hosting the mirror. FTP access is not accepted.

The virusdb team will use the account only to update the virus database.

## Public Mirror vs Private Mirror

If you cannot make your mirror available to all ClamAV users, and you would like to serve the CVD update to your network from a local cache, you should read CvdPrivateMirror

## How to

Here is a step-by-step guide to become a mirror

### Request a rsync account

Before starting the setup, [contact Joel Esler](http://www.clamav.net/about/team) to verify that there is a need for another mirror in your country.

### Configure your web server

Set up a virtual host for http://database.clamav.net, http://db.*.clamav.net and http://clamav.your-domain.tld. Note there is an asterisk in the second hostname. A literal asterisk. Do not replace it with your country code. If you are using name based virtual hosts, you can check whether the mirror setup is correct or not, with the following commands (replace 1.2.3.4 with the actual ip address of your web server):
<pre>echo Success &gt;~clamavdb/public_html/local_test
chmod 644 ~clamavdb/public_html/local_test
wget -O - --header="Host: db.test.clamav.net" 1.2.3.4/local_test
</pre>

If the output is "Success", then your Apache is configured correctly.

More information on name-based vhosts under Apache is available at http://httpd.apache.org/docs/mod/core.html#serveralias .

Here is an example for a typical setup:
<pre>&lt;VirtualHost 1.2.3.4:80&gt;
ServerAdmin john@clamav.foo.com
DocumentRoot /home/users/clamavdb/public_html
ServerName database.clamav.net
ServerAlias db.*.clamav.net
ServerAlias clamav.foo.com
# following 2 lines are for Apache 2.x only
LogFormat "%h %l %u %t \"%r\" %&gt;s %O \"%{Referer}i\" \"%{User-Agent}i\"" combinedrealsize
CustomLog /path/to/clamav-access.log combinedrealsize
&lt;/VirtualHost&gt;
</pre>

If you are using nginx:
<pre> server {
        listen       1.2.3.4:80;
        server_name  database.clamav.net ~^db\..*\.clamav\.net clamav.foo.com;
        location / {
          root /home/users/clamavdb/public_html;
          index index.html;
        }
    }
</pre>

If you are using lighttpd:
<pre>$HTTP["host"] =~ "^((database|db\..+)\.clamav\.net|clamav\.foo\.com)$" {           
       var.basedir    = "/home/users/clamavdb"
       server.document-root   = basedir + "/public_html"                       
       accesslog.filename     = "/var/log/lighttd/clamav.foo.com.access.log"          
       index-file.names       = ( "index.html" )                             
       $HTTP["url"] =~ "\.cvd$" {                                             
               connection.kbytes-per-second = 200                             
       }                                                                      
}                                         
</pre>

If you cannot create wildcard vhosts, you must use IP based virtual hosts! Please note that an http redirect (e.g. RedirectPermanent) is not enough! freshclam can't handle redirects yet.

### Create a system account

Create an account with login "clamavdb" and give it write access to the virtual host's DocumentRoot.

You may want to disable password authentication for this account and change the password to something obscure.

The "clamavdb" user's shell must be /bin/sh or /bin/bash . Otherwise the user won't be able to run the command associated with the ssh public key.

Take a look at the content of "authorized_keys_noshell":
<pre>no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty,command="bash ~/bin/clam-clientsync rsync1.clamav.net &" ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxxx clamavdb@morgana
no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty,command="bash ~/bin/clam-clientsync rsync2.clamav.net &" ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxxx clamavdb@morgana
</pre>

The only command which can be executed by the owner of the corresponding ssh private key is ~/bin/clam-clientsync. We will only be able to trigger the execution of that script and nothing else!

If you would like to grant us shell access, use authorized_keys_shell instead. That file contains Luca Gibelli ssh public key too.

### Download scripts and config. files

Download the following files:

  * [authorized_keys_noshell.sig](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/authorized_keys_noshell.sig.txt)

  * [authorized_keys_noshell](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/authorized_keys_noshell.txt)

  * [authorized_keys_shell.sig](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/authorized_keys_shell.sig.txt)

  * [authorized_keys_shell](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/authorized_keys_shell.txt)

  * [clam-clientsync.conf](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/clam-clientsync.conf.txt)

  * [clam-clientsync](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/clam-clientsync.txt)

from [github mirror files](https://github.com/vrtadmin/clamav-faq/blob/master/mirrors/files/).

Verify the signature using:
<pre>$ gpg --verify authorized_keys_noshell.sig authorized_keys_noshell
$ gpg --verify authorized_keys_shell.sig authorized_keys_shell</pre>

The VRT PGP public key is available on most [keyservers like this one] (http://pgp.mit.edu:11371/pks/lookup?search=Sourcefire+VRT&op=index) and on [VRT Labs] (http://labs.snort.org/contact.html). It can eventually be verified by telephone. Contact us by email first.

If you don't want to give us shell access, copy authorized_keys_noshell to ~/clamavdb/.ssh/authorized_keys:
<pre>$ cp authorized_keys_noshell ~/.ssh/authorized_keys
</pre>

If you want to give us shell access, use authorized_keys_shell instead:
<pre>$ cp authorized_keys_shell ~/clamavdb/.ssh/authorized_keys</pre>

In both cases you have to make sure that files and directories have proper permissions:
<pre>$ chmod 755 ~clamavdb
$ chmod 700 ~clamavdb/.ssh
$ chmod 600 ~clamavdb/.ssh/authorized_keys
</pre>

Copy clam-clientsync to ~clamavdb/bin/ and clam-clientsync.conf to ~clamavdb/etc/:
<pre>chmod 755 ~clamavdb/bin/clam-clientsync
chmod 600 ~clamavdb/etc/clam-clientsync.conf</pre>

Everything must be owned by user clamavdb.
<pre>chown clamavdb ~clamavdb/bin/clam-clientsync
chown clamavdb ~clamavdb/etc/clam-clientsync.conf
chown -R clamavdb ~clamavdb/.ssh
chown clamavdb ~clamavdb/etc
</pre>

The clam-clientsync requires the "lockfile" program, which is part of the procmail package. Before going any further, please check that "lockfile" is available.

### Submit the mirror details

Send an email with the server's details to luca -at- clamav.net following this template:
<pre>
   * clamav.foo.com
   * ip address: 1.2.3.4
   * country: Poland
   * admin: John Doe &lt;john.doe@foo.com&gt;
</pre>
Our monitoring system will send e-mail notifications to the email address specified under "admin".

If you would to receive notifications at a different address (e.g. alerts@foo.com), please use the following template:
<pre>
   * clamav.foo.com
   * ip address: 1.2.3.4
   * country: Poland
   * admin: John Doe &lt;john.doe@foo.com&gt; || &lt;alerts@foo.com&gt;
</pre>

### Modify configuration file

Edit ~clamavdb/etc/clam-clientsync.conf . If your DocumentRoot is /home/users/clamavdb/public_html, your login is foo and your password guessme, then your clam-clientsync.conf will look like this:
<pre>TO=/home/users/clamavdb/public_html
RSYNC_USER=foo
RSYNC_PASSWORD=guessme
MODULE="clamavdb"
EXCLUDE="--exclude local_*"
</pre>

### Update Firewall

Reconfigure your packet filter to allow incoming connections on port 22/tcp and outgoing connections to ports 873/tcp and 873/udp.

You can furtherly restrict access to these ports by only allowing connections from/to the following IP addresses:

   * 194.109.142.194
   * 64.18.103.6
   * 78.46.32.131

Any changes to this IP address list will be announced on the clamav-mirrors mailing-list.

Please note that we do NOT support mirrors running ssh on a non standard port. If you are running ssh on a non standard port, you must add a redirect for packets with source address 194.109.142.194, 64.18.103.6, 78.46.32.131 and destination port 22 to the port where ssh is listening to.

Here is an example for a Linux box running ssh on port 2222 and without any firewall blocking packets on port 22:
<pre>
   * iptables -t nat -I PREROUTING -s 194.109.142.194 -m tcp -p tcp --dport 22 -j REDIRECT --to-port 2222
   * iptables -t nat -I PREROUTING -s 64.18.103.6 -m tcp -p tcp --dport 22 -j REDIRECT --to-port 2222
   * iptables -t nat -I PREROUTING -s 78.46.32.131 -m tcp -p tcp --dport 22 -j REDIRECT --to-port 2222
</pre>
YMMV.

Make sure to save these rules so that they are executed everytime you reboot the system

### Protect your mirror from abusers

You should check out the [[MirrorsCoordination]] page for information about protecting your mirror from abusers. If your mirror has strict bandwidth/resources you will find some useful hints on that page.

### Add your logo

You are welcome to put your company logo on the mirror home page. Just copy it to the DocumentRoot and rename it to "local_logo.png". The index.html is unique for every mirror. Please note that any file in the DocumentRoot whose name doesn't match "local_*" will be deleted at every mirror sync.

### Subscribe to the mirrors mailing lists

Subscribe to clamav-mirrors at lists.clamav.net: http://lists.clamav.net/mailman/listinfo/clamav-mirrors

Subscribe requests have to be approved. We will approve your subscription request only after reviewing your server's info.

When everything is done, your server's IP address will be added either to your country's dns record (db.XY.clamav.net) or one of the round robin record and your company will be listed on our mirrors list page.

## Statistics

Although it's not required, we really appreciate if you can make access statistics of your mirror available to us. They should be available at http://your-mirror-host-name/local_stats/ and they must be protected with login and password. You should use the same login and password you are using in your ~clamavdb/etc/clam-clientsync.conf file.

If possible, please tell your statistics generator to ignore requests made by the "ClamAV-MirrorCheck" agent.

If you are using Webalizer, you can add the following directive to your conf. file:

HideAgent ClamAV-MirrorCheck

If you are using AWStats, you can add this one instead:

SkipUserAgents="ClamAV-MirrorCheck"

Refer to your stats generator's manual for more info.

Important note for Apache2 users:

As stated in the Apache documentation at http://httpd.apache.org/docs/2.0/mod/mod_log_config.html please note that in httpd 2.0, unlike 1.3, the %b and %B format strings do not represent the number of bytes sent to the client, but simply the size in bytes of the HTTP response (which will differ, for instance, if the connection is aborted, or if SSL is used).

The %O format provided by mod_logio will log the actual number of bytes sent over the network.

## Admin's duty

Scheduled downtimes should be announced on the clamav-mirrors mailing-list in advance.

   * IP address changes should be notified in advance too.</p>
   * Changes in the ssh host public key of the mirror host should be announced on the clamav-mirrors mailing-list.</p>
   * It is essential to be able to contact the sysadmin responsible for the mirror server and get a quick response. Whenever a problem with a mirror occurs we need to immediately find out its cause and act consequently.

## Mirror status

Every mirror is continuously monitored to ensure that every ClamAV user gets the latest virus database.

Every three hours we upload a file called timestamp on every mirror. Every hour we choose a random mirror and check that timestamp is fresh. If the file is one day old or unavailable, the mirror if marked as "old" and the ClamAV team receive a warning. If the situation persists for two days, the mirror is temporarily removed from the list.

You can view the current status of every ClamAV database mirror at http://www.clamav.net/mirrors.html.

Please note that this page doesn't reflect how often the database is propagated to mirrors. It just shows the trend of mirrors availability.

# About CVDs and freshclam

## Who is responsible for the virus database

The virusdb team, aka [Sourcefire VRT](http://www.clamav.net/about/team) take care of reviewing virus signatures, checking for new viruses in the wild and committing changes to the virus database file.

The updates are released quite often (usually a few times per day). If you want to be notified whenever the virus database is updated subscribe to [clamav-virusdb at lists.clamav.net](http://lists.clamav.net/mailman/listinfo/clamav-virusdb) =.=

Every time the virusdb team updates the database, an email with all the details about the update is automatically posted to this mailing-list.

## Virus submission

Whenever you find a new virus which is not detected by ClamAV you should send it to the virusdb team by filling the form at http://www.clamav.net/sendvirus.

They will review your submission and update the database so that the whole ClamAV user community can take benefit from it. Never send virus samples to ClamAV mailing-lists or developers addresses.

## Redirecting users to the closest mirror

Our users are encouraged to add the following directives to their freshclam.conf :

   * DatabaseMirror db.XY.clamav.net
   * DatabaseMirror db.local.clamav.net

where XY stands for the country the server lives in a full list is available at http://www.iana.org/cctld/cctld-whois.htm. Each db.XY.clamav.net DNS record points to the mirrors available in the corresponding country. For a complete list of the mirrors available in each country visit http://www.clamav.net/mirrors.html.

If freshclam can't connect to db.XY.clamav.net, it will fallback on db.local.clamav.net, which attempts to redirect the user to the closest pool of mirrors by looking up its ip source address in GeoIP database (http://www.maxmind.com/app/geoip_country).See:

   * http://www.iana.org/assignments/ipv4-address-space
   * http://ip-to-country.webhosting.info/
   * http://ftp.apnic.net/stats/apnic/
   * http://www.ripe.net/db/erx/erx-ip/

We are aware that looking up the IP source address is not an accurate method to find the user location from a network topology point of view. However we believe it is a good approximation.

## Getting a copy of the latest virus database

The most important factor for an antivirus's efficiency is to be up to date. ClamAV comes with a tool to update the virus database automatically: its name is freshclam.

freshclam looks up the TXT record associated with current.cvd.clamav.net and extracts the latest database version available from the string returned. If the local database is outdated, freshclam tries to connect to the hostnames listed in freshclam.conf (DatabaseMirror directive). If the first server in the list fails or the latest database is not available on that mirror (e.g. in case there has been a problem sync'ing the mirror), freshclam will sleep for 10 secs and then try again with the next one, and so on.

After freshclam downloads the new database, it sends a notify to clamd (if active) to reload the database.

It is important for the machine running ClamAV to be able to make DNS lookups and to connect to port 80 of external hosts on Internet either directly or through a proxy. There are known problems with some transparent proxies caching what they shouldn't cache. If you should run into this kind of problem, please check your proxy configuration before reporting a bug.

