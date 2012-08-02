# FAQ - Safebrowsing #

## Official FAQ ##

This is the official FAQ. For additional FAQs please visit our [GitHub repository](https://github.com/vrtadmin/clamav-faq) . You are encouraged to contribute to them.

ClamAV 0.95 introduced support for Google Safe Browsing database.
The database is packed inside a CVD file and distributed through our mirror network as <a href="http:	//db.local.clamav.net/safebrowsing.cvd">safebrowsing.cvd</a> .
This feature is disabled by default on all installations and should be enabled with extreme care.

All signatures provided by Google Safe Browsing Database will be prefixed with the _Safebrowsing_ tag. If ClamAV reports <code>Safebrowsing.&lt;something&gt; FOUND</code>, it means that the <a href="http://code.google.com/support/bin/answer.py?answer=70015" target="_blank">advisory was provided by Google</a> and not by ClamAV Virus database.

Please note that such reports __DO NOT__ necessarily mean that the data scanned contains some malware. You should treat such data as a __potential__ risk, that is a __suspicious__ source of malware.

If you want to know more about the __potentially__ dangerous data matched by the signature, you should visit <a href="http://www.antiphishing.org/" target="_blank">http://www.antiphishing.org/</a> (for phishing warnings) or <a href="http://www.stopbadware.org/" target="_blank">http://www.stopbadware.org/</a> (for malware warnings). 

In order to enable this feature, you must add "SafeBrowsing Yes" to freshclam.conf . 
There is no option in clamd.conf. If the engine finds Google Safe Browsing files in the database directory, ClamAV will enable safe browsing. To turn it off you need to update freshclam.conf and remove the safebrowsing files from the database directory before restarting clamd.                                                    
