# FAQ - CVD #

## Official FAQ ##

This is the official FAQ. For additional FAQs please visit our [GitHub repository](https://github.com/vrtadmin/clamav-faq) . You are encouraged to contribute to them.

## Updating the ClamAV Virus Database (CVD) ##

* What does _WARNING: DNS record is older than 3 hours_ mean?

>freshclam attempts to detect potential problems with DNS caches and  switches to the old mode if something looks suspicious. If this message appears seldomly, you can safely ignore it. If you get the error everytime you run freshclam, check your system clock. If it is set correctly, check your dns settings. If those didn't help, try putting this at the top of your cronjob:<pre> host -t txt current.cvd.clamav.net; perl -e 'printf "%d\n", time;' </pre> The 4th field of the first line should be less than 3 &lowast; 3600 behind the output of the second line. If not, you have a caching DNS server somewhere  misbehaving.

* How often is the virus database updated?

>The virus database is usually updated many times per week. Check out "http://lurker.clamav.net/list/clamav-virusdb.html":http://lurker.clamav.net/list/clamav-virusdb.html to see our response times to new threats. The virusdb team tries to keep up with the latest worm  in the wild. When a new worm spreads out, often it is  less than one hour before we release a database update. You can contribute to make the virusdb updating process  more efficient by submitting samples of viruses  via our "web interface":submit.

* How many times per hour shall I run freshclam?

>You can check for database update as often as 4 times per hour provided that you have the following options in freshclam.conf: _DNSDatabaseInfo current.cvd.clamav.net_ _DatabaseMirror db.XY.clamav.net_ _DatabaseMirror database.clamav.net_ Replace XY with your "country code":iana.  If you don't have that option, then you must stick with 1 check per hour.

* The last CVD update crashed my ClamAV installation. Why?

>Before publishing a CVD update, we verify that it can be correctly loaded by the last two stable release series of ClamAV.

* The last CVD update detects a lot of false positives on my system. Why?

>Before publishing a CVD update, we test it for false positives using the latest stable release of ClamAV. If you want to avoid problems with false positives, you must run the latest stable version of ClamAV.

* I tried to submit a sample through the web interface, but it said the sample is already recognized by ClamAV. My clamscan tells me it's not. I have already updated my database and ClamAV engine, what's wrong with my setup?

>Please run clamscan with the <code>--detect-broken</code> option. Also  check that freshclam and clamscan are using the same path for storing/reading the database.

* I found an infected file in my HD/floppy/mailbox, but ClamAV doesn't recognize it yet. Can you help me? 

>Our virus database is kept up to date with the help of the community. Whenever you find a new virus which is not detected by ClamAV you should  "complete this form":submit. The virusdb team will review your submission and update the database if necessary. Before submitting a new sample: - check that the value of DatabaseDirectory, in both clamd.conf and freshclam.conf, is the same - update your database by running freshclam

* How do I keep my virus database up to date?

>ClamAV comes with _freshclam_, a tool which periodically checks for new database releases and keeps your database up to date.

* I get this error when running freshclam: _Invalid DNS reply. Falling back to HTTP mode_ or _ERROR: Can't query current.cvd.clamav.net_ . What does it mean?

>There is a problem with your DNS server. Please check the entries in /etc/resolv.conf and verify that you can resolve the TXT record manually: <code>$ host -t txt current.cvd.clamav.net</code> If you can't, it means your network is broken. You'll be still able to download the updates, but you'll waste a lot of bandwidth checking for updates.

* I get this error when running freshclam: _ERROR: Connection with ??? failed_ . What shall I do?

>Either your dns servers are not working or you are blocking port 53/tcp. You should manually check that you can resolve hostnames with: <code>$ host database.clamav.net</code>. If it doesn't work, check your dns settings in /etc/resolv.conf. If it works, check that you can receive dns answers longer than 512 bytes, e.g. check that your firewall is not blocking packets which originate from port 53/tcp. An easy way to find it out is: <code>$ dig @ns1.clamav.net db.us.big.clamav.net</code>

* How do I know if my IP address has been blacklisted?

>Try to download daily.cvd with lynx or wget from the same machine that is running freshclam. Future versions of freshclam will provide a better way to deal with this.

* What is the mirrors.dat file?

>mirrors.dat is used by freshclam to keep track of broken mirrors. It avoids the unnecessary delays caused by trying to download a CVD update from a mirror which failed multiple times during the last 24 hours.

* I'm running ClamAV on a lot of clients on my local network. Can I serve the cvd files from a local server so that each client doesn't have to download them from your servers?

>Sure, you can find more details on our wiki at "http://wiki.clamav.net/Main/CvdPrivateMirror":http://wiki.clamav.net/Main/CvdPrivateMirror

>If you want to take advantage of incremental updates, install a proxy server and then configure your freshclam clients to use it (watch for the HTTPProxyServer parameter in man freshclam.conf). 

>The second possible solution is to configure a local webserver on one of your machines (say machine1.mylan) and let freshclam download the *.cvd files from http://database.clamav.net to the webserver's DocumentRoot. Add this line to freshclam.conf on machine1.mylan: __ScriptedUpdates off__. First the database will be downloaded to the local webserver and then the other clients on the network will update their copy of the database from it. For this to work you have to change freshclam.conf on your clients so that it reads:
> 
>__DatabaseMirror machine1.mylan__
>
>__ScriptedUpdates off__

* I can't wait for you to update the database! I need to use the new signature NOW!

>No problem, save your own signatures in a text file with the appropriate extension (see "signatures.pdf":/doc/latest/signatures.pdf for more information). Put it in the same dir where the .cvd files are located. ClamAV will load it after the official .cvd files. You need not to sign the .db file.

* Can I download the virusdb manually?

>Yes, the virusdb can be downloaded from the _Latest releases_ section on our home page.

* I can't resolve current.cvd.clamav.net! Is there a problem with your/my DNS servers?

>current.cvd.clamav.net has got only a TXT record, not a type A record! Try this command: <code>$ host -t txt current.cvd.clamav.net</code>. Please note that some not RFC compliant DNS servers (namely the one shipped with the Alcatel (now Thomson) SpeedTouch 510 modem) can't resolve TXT record. If that's the case, please recompile ClamAV with the flag <code>--enable-dns-fix</code> .
