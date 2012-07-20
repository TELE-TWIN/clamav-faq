# FAQ - Troubleshooting #

>The virus database is usually updated many times per week. Check out "http:	//lurker.clamav.net/list/clamav-virusdb.html":http://lurker.clamav.net/list/clamav-virusdb.html to see our response times to new threats. The virusdb team tries to keep up with the latest worm  in the wild. When a new worm spreads out, often it is  less than one hour before we release a database update. You can contribute to make the virusdb updating process  more efficient by submitting samples of viruses  via our "web interface":submit.

* How many times per hour shall I run freshclam?

>If you are running ClamAV 0.7x please **upgrade NOW**. If you are running ClamAV 0.8x or later, you can check for database update as often as 4 times per hour provided that you have the following options in freshclam.conf: _DNSDatabaseInfo current.cvd.clamav.net_ _DatabaseMirror db.XY.clamav.net_ _DatabaseMirror database.clamav.net_ Replace XY with your "country code":iana.  If you don't have that option, then you must stick with 1 check per hour.

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

>Sure, there are two possible solutions.

>>If you want to take advantage of incremental updates, install a proxy server and then configure your freshclam clients to use it (watch for the HTTPProxyServer parameter in man freshclam.conf). 

>>The second possible solution is to configure a local webserver on one of your machines (say machine1.mylan) and let freshclam download the *.cvd files from http://database.clamav.net to the webserver's DocumentRoot. Finally, change freshclam.conf on your clients so that it reads: 
 __DatabaseMirror machine1.mylan__ 
First the database will be downloaded to the local webserver and then the other clients on the network will update their copy of the database from it. For this to work, you have to add __ScriptedUpdates off__ on all of your machines!

* I can't wait for you to update the database! I need to use the new signature NOW!

>No problem, save your own signatures in a text file with the appropriate extension (see "signatures.pdf":/doc/latest/signatures.pdf for more information). Put it in the same dir where the .cvd files are located. ClamAV will load it after the official .cvd files. You need not to sign the .db file.

* Can I download the virusdb manually?

>Yes, the virusdb can be downloaded from the _Latest releases_ section on our home page.

* I can't resolve current.cvd.clamav.net! Is there a problem with your/my DNS servers?

>current.cvd.clamav.net has got only a TXT record, not a type A record! Try this command: <code>$ host -t txt current.cvd.clamav.net</code>. Please note that some not RFC compliant DNS servers (namely the one shipped with the Alcatel (now Thomson) SpeedTouch 510 modem) can't resolve TXT record. If that's the case, please recompile ClamAV with the flag <code>--enable-dns-fix</code> .

Last update: Apr 7, 2009
[lang_xx]<p style="color:red;font-size:x-small">We do our best to keep the localized version of this website up to date, however that is not always possible. Please refer to the English version for the latest information.</p>[/lang_xx]
[lang_it]<p style="color:red;font-size:x-small">Facciamo il nostro meglio per mantenere aggiornate le versioni localizzate di questo sito ma, nonostante i nostri sforzi, alle volte ciò non è sempre possibile. È necessario fare riferimento alle pagine in inglese per avere le informazioni più aggiornate.</p>[/lang_it]
[lang_ja]<p style="color:red;font-size:x-small"> ※我々は日本語翻訳版を最新に保つよう最大限の努力をしています。しかし、必ずしも常に最新であるとは限りません。英語版のページで最新の情報を確認してください。</p>[/lang_ja]
[lang_de]<p style="color:red;font-size:x-small">Wir geben unser Bestes um die Übersetzungen dieser Webseite stets aktuell zu halten, leider ist das nicht immer möglich. Bitte beziehe Dich für die neusten Informationen immer auf die Englische Version.</p>[/lang_de]
[lang_es]<p style="color:red;font-size:x-small">Intentamos disponer actualizada la versión traducida de este sitio web, sin embargo no siempre es posible. Por favor recurra a la versión en Inglés para la última información</p>[/lang_es]
[lang_tc]<p style="color:red;font-size:x-small">此語言版本未能提供有關內容，謹此致歉。</p>[/lang_tc]
[lang_sc]<p style="color:red;font-size:x-small">我们会尽量保持更新此网站的本地化版本。如有延误，请参阅英文版，以便获取最新信息。</p>[/lang_sc]
[lang_fr]<p style="color:red;font-size:x-small">Nous faisons de notre mieux pour tenir la version localisée de ce site à jour, mais ce n'est cependant pas toujours possible. Veuillez vous réferer à la version anglaise pour les toutes dernières informations</p>[/lang_fr]
[lang_pl]Robimy co w naszej mocy, by przetłumaczona wersja tej strony była jak najświeższa, ale nie zawsze jest to możliwe. Odwiedź angielską wersję, by uzyskać najnowsze informacje.</p>[/lang_pl]
[lang_ru]<p style="color:red;font-size:x-small">Мы делаем всё зависящее от нас, чтобы сохранять актуальной локализованную версию этого веб-сайта, однако это не всегда возможно. Пожалуйста, смотрите последнюю информацию в английской версии.</p>[/lang_ru]
[lang_hu]<p style="color:red;font-size:x-small">Azon fáradozunk, hogy ez a weblap a lehető legfrissebb legyen, ám ez szinte lehetetlen. Kérjük, az angol felületen keresse a legújabb információkat!</p>[/lang_hu]
[lang_pt]<p style="color:red;font-size:x-small">Fazêmos o melhor para manter uma versão traduzida do nosso sítio actualizada, no entanto isso nem sempre é possível. Por favor consulte a versão em Inglês para obtêr a versão mais actualizada.</p>[/lang_pt]
[lang_nl]<p style="color:red;font-size:x-small">We doen ons best om de gelocaliseerde versie van deze website up to date te houden. Dat is echter niet altijd mogelijk. Bekijk alstublieft de Engelse versie voor de recentste informatie.</p>[/lang_nl]
[lang_ro]<p style="color:red;font-size:x-small">Încercăm să menținem actualizată traducerea pentru acest sit web, dar acest lucru nu este întotdeauna posibil. Te rugăm să consulți versiunea în Engleză pentru ultimele informații apărute.</p>[/lang_ro]

