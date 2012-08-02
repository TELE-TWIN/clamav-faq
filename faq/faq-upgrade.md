# FAQ - Upgrading #

## Official FAQ ##

This is the official FAQ. For additional FAQs please visit our [GitHub repository](https://github.com/vrtadmin/clamav-faq) . You are encouraged to contribute to them.

### Upgrading ClamAV

* How do I upgrade ClamAV?

>Visit our [Upgrade Instructions] page.

* What does _WARNING:	Current functionality level = 1, required = 2_ mean?

>The _functionality level_ of the database determines which scanner engine version is required to use all of its signatures. If you don't upgrade immediately you will be missing the latest viruses.

* What does _Your ClamAV installation is OUTDATED_ mean?

>You'll get this message whenever a new version of ClamAV is released.  In order to detect all the latest viruses, it's not enough to keep your  database up to date. You also need to run the latest version of the  scanner. You can download the "sources":/download/sources of the latest release from our website. Upgrade instructions are on the "Wiki":http://wiki.clamav.net/Main/UpgradeInstructions. If you are afraid to break something while upgrading, use  the "precompiled packages":/download/packages/ for your operating system/distribution.  Remember: running the latest stable release also improves stability.

* I upgraded to the latest stable version but I still get the message _Your ClamAV installation is OUTDATED_, why?

>Make sure there is really only one version of ClamAV installed on your system: 
   <pre><code>   $ whereis freshclam 
   $ whereis clamscan</code></pre>

>Also make sure that you haven't got old libraries (libclamav.so&#42;) lying around your filesystem. You can verify it using: <code>$ ldd `which freshclam`</code>

* What does _Malformed hexstring: This ClamAV version has reached End of Life_ mean?

>Please refer to: [eol-clamav-094]

* How do I verify the integrity of ClamAV sources?


>Using [GnuPG] you can easily verify the authenticity of your stable release downloads by using the following method: Download the [Sourcefire VRT key] from the clamav.net site. Import the key into your local public keyring: <code>$ gpg --import vrt.gpg</code>. Download the stable release AND the corresponding .sig file to the same directory. Verify that the stable release download is signed with the Sourcefire VRT key: <br><code>$ gpg --verify clamav-X.XX.tar.gz.sig</code><br>Please note that the resulting output should look like the following:<br> <pre> <code>
gpg: Signature made <some date> using DSA key ID 15497F03
gpg: Good signature from Sourcefire VRT <email address>
</code></pre>

>For other PGP implementation, please refer to their manual.

* Where can I get the latest SVN snapshot of ClamAV?

>Visit the [source download page].

* Is my compiler/hardware/operating system supported by ClamAV?

>ClamAV supports a wide variety of compilers, hardware and operating systems. Our core compiler is gcc with Linux on 32 and 64 bit Intel platforms, though we also test using other compilers, including Sun's C compiler, Microsoft's Visual Studio, Intel's C compiler, LLVM-GCC, and others. To date we have only found one compiler that we do not support, GCC version 4.0.0 to 4.0.1 inclusive. We have found that version of the compiler produces incorrect code on all of the platforms and operating systems on which we have tested it. ClamAV will not work using that compiler and you MUST switch to an alternative, such as GCC3.4 or GCC4.1. Please contact your vendor for further information. Please refer to [gcc's bugzilla] for further information. If you want to see a proof of why gcc 4.0.1 generates wrong code for the kernel read the [relevant article] on kerneltrap. More information about this bug is also available in [our bugzilla]. Our configure scripts will detect if your compiler is affected by this bug and refuse to generate a non working binary with the following error message: _your compiler has gcc PR26763-2 bug, use a different compiler_ . If you are on MacOS X, you can try an alternative compiler, LLVM-GCC4.2-2.2, which has [official binaries available]


[Upgrade Instructions]: /Upgrading.html
[eol-clamav-094]: http://www.clamav.net/eol-clamav-094
[GnuPG]:http://www.gnupg.org/
[Sourcefire VRT key]: /gpg/vrt.gpg
[source download page]: /lang/en/download/sources/
[gcc's bugzilla]: http://gcc.gnu.org/bugzilla/show_bug.cgi?id=26763
[relevant article]: http://kerneltrap.org/Linux/Compiler_Optimization_Bugs_and_World_Domination
[our bugzilla]: https://wwws.clamav.net/bugzilla/show_bug.cgi?id=613
[official binaries available]: http://llvm.org/releases/download.html#2.2
