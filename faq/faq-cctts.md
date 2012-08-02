# FAQ - CCTTS #

## Official FAQ ##

This is the official FAQ. For additional FAQs please visit our [GitHub repository](https://github.com/vrtadmin/clamav-faq) . You are encouraged to contribute to them.

* What is it?

>The ClamAV Community Threat Tracking System sends summary data to our server about the malware detected on your system. Freshclam usually sends the data when you update your signatures. You can also use freshclam’s --submit-stats option to submit statistics without updating the signature database.

* How do you use the data you collect?

>The ClamAV team and Sourcefire VRT use the data to determine the most active malware in real-time and to monitor activity based on historical trends. We will make all of our findings public.

* Can I see the results?

>Yes! The amount of analysis that we can publish depends on the amount of data that we receive. We need enough data to have confidence in any conclusions drawn; so as more users submit more data we can publish more analyses.


* How do I enable Anonymous Statistics?

>Look in your freshclam.conf (usually located in /usr/local/etc/freshclam.conf) for the entry “SubmitDetectionStats” and ensure it is enabled and points to your clamd.conf, for example “SubmitDetectionStats /usr/local/etc/clamd.conf”. When you enable SubmitDetectionStats freshclam will fetch the latest statistics from clamd and submit them to our server.

* How can I see statistics regarding the malware detected by my own ClamAV installation?

>Get an HostID (see following FAQs) for each of your ClamAV installations and add the directive "DetectionStatsHostID XXXX" (where XXXX is your HostID) to your freshclam.conf. You will be able to view the data submitted by your ClamAV installation anytime by logging on <a href="http:	//www.stats.clamav.net">http://www.stats.clamav.net</a>.

* What is an HostID?

>An HostID is a unique identifier which helps us tracking data submissions from individual ClamAV installations. 

* How do I get an HostID?

>You can get your own HostID by logging on <a href="http://www.stats.clamav.net">http://www.stats.clamav.net</a> and clicking on "Add new host"

* How does Freshclam send data?

>Freshclam sends the data to stats.clamav.net using HTTP (POST) port 80.

* What data does freshclam actually submit?

>File name, malware name, and time of detection for each malware that is detected.

* Does Freshclam send private data?

>None, other than the public IP address that freshclam is using to contact our server.

* Do you send any of my data to third parties?

>No. We only make our trend analyses available to third parties. We do not charge for these analyses, we provide them as a service to the Open Source Community.

* Why should I contribute data?

>The more data that we receive, the better the quality of the analyses since the analyses are based on more statistically significant data sets. You will be contributing back to the Open Source Community.

* Do other AV vendors do this?

>Yes. It is common practice within the security industry to gather and use such data.

* What is the size of transmitted data?

>To optimize the submission process and to reduce the load on our servers, freshclam submits the data in sets of 10 records, up to 50 in one session. Thus if you have 45 new records, then it will submit 40; if you have 78 then it will submit the latest 50 entries; and if you have only 9 records freshclam won't submit any.

* I am not running clamd; can I still use this feature?

>Clamd’s logfile is the data source (see the LogFile setting in clamd.conf). In principle, any program that writes correct records to that file will generate data usable by freshclam, but most users will need to be using clamd to make use of this feature. For example, freshclam will not submit malware detected by clamscan because clamscan does not write to the logfile.
