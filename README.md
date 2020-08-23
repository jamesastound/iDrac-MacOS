# iDRAC-MacOS
A Guide to Accessing iDRAC5 / iDRAC6 on MacOS

# I have tested this method with...
* [Java](https://www.java.com/en/download/mac_download.jsp) -  `jre-8u261-macosx-x64.dmg`
* macOS Mojave `10.14.6`
* macOS Catalina `v10.15.6` (live and [Parallels VM](https://www.parallels.com/products/desktop/pro/));
* macOS Big Sur `v11.0 Public Betas 1 & 2`
* iDRAC6 Enterprise installed in a Dell PowerEdge R510 w/RAC Firmware version `2.90 (Build 04)` 
* iDRAC5 instaelled in a Dell PowerEdge 2950 Mk. II w/RAC Firmware version `1.65 (12.08.16)`

# Edit java.secirty
* The iDRAC jar files are signed with old versions of Java, which are no longer available. More modern Java versions have blocked old protocols (MD5 and SSLv3) in their configuration files. It is necessary to edit or replace the existing `java.security` configuration file to re-enable these features.
* Default `java.security` file location `"/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security"`
  * If needed, find non-default locations using `sudo find /Library -name "java.security"`
* Rename the existing `java.security` file `sudo mv "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security"` `"/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security.original"`
* Replace with `java.security` from this site.
* Essentially we are commenting out or removing the lines of code that disable MD5 and SSLv3 features in Java. Doing so will make your install more vulnerable to other Java Applications, so do so at your own risk.
* You can see the changes I've made with `diff "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security" "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security.original"`

# Add Exception in Java Control Panel
* Goto System Settings > Java to open the Java Control Panel
* Under the Security Tab click `Edit Site List...` and then click Add to enter your iDRAC to the list as `https://000.000.000.000`, replacing zeros with your iDRAC IP Address. Be sure to use **https**, not http. Click OK when complete.
* Other settings on this tab should have "Enable Java content..." checked and Security Level: "High." These should be the install defaults.

# Connect to iDRAC
* Open Safari, or your favorite web browser and navigate to your iDRAC IP Address.
* Login and click the `Console` tab (iDRAC5) or `Console/Media` tab (iDRAC6)
* The Plug-in Type should be set to `Java (Auto Configured)` or `Java (User Configured)`. If not, click the Configuration tab and change it. If you don't see Java as an option, be sure you have the latest DRAC Firmware installed.
* On iDRAC5, click `Connect` and on iDRAC6 click `Launch Virtual Console`
* In Safari, this will download a file `vkvm.jnlp` on IDRAC5 or `viewer.jnlp` on iDRAC6.
* Click the `.jnlp` file to launch the virtual console.
* macOS will likely force you to open System Preferences > Security & Privacy and click `Open Anyway` on the Genral tab.
* macOS will then warn it cannot verify the developer of the <filename>. Click `Open`
* Java will then warn "The certificate is not valid...". Click `Continue`
* Java will also ask "Do you want to run this application?" Click `Run`
* Finally, the iDRAC Console should appear.

# Troublshooting Connection Problems
* It can often take a minute or two before the console shows and image. During that time, you may see No Signal. You may see No Signal sporadically if your connection is poor. You can try hitting a key like Enter to wake up the display. If your system is set to turn off the monitor after a period of time, it will revert to No Signal.
* If you get an `Out of Range` error, the remote resolution of your monitor is too high. Ideally your OS should boot to 1024x768.
  * I prefer setting screen resolution in [Grub](https://askubuntu.com/questions/54067/how-do-i-safely-change-grub2-screen-resolution).
* If you never make it to the Console, it is possible macOS or Java changed something since I wrote this. Try going to the Java Control Panel (System Settings > Java), click the Advanced Tab and choose `Show console` under the `Java Console` option. Click Apply.
* Excellent Discussion (primarily focused on iDRAC6) can be found on [xbb](https://gist.github.com/xbb)/[README](https://gist.github.com/xbb/4fd651c2493ad9284dbcb827dc8886d6) gist [here](https://gist.github.com/xbb/4fd651c2493ad9284dbcb827dc8886d6).