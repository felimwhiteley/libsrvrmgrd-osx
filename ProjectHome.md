Apple OS X **Servers** run a command and control daemon on port 311 which uses XML to send and receive commands and report status. This is the native protocol used by Apple's own Server Manager application and talks over HTTPS. Using this library it's possible to get status updates from a server and send commands to restart services etc.

This code can be run from Linux (theoretically any OS with Python 2.5+), Mac OS X and it should run with Windows also. It can talk to **_server_** versions of **Panther**, **Tiger**, **Leopard**, **Snow** **Leopard**, **Lion** & **Mountain** **Lion**  (Older Versions May Work But I Have No Access To Them).

**_Call For Help For Strange Behaviour On 10.8.2:_** There is an Apple discussion post by one of our contributors, John, who has noticed strange behaviour with the latest updates from Apple. Please post to the page if you have any insights, a case is open with Apple as it is reproducible using only the web interface, but any help would be appreciated. https://discussions.apple.com/message/19978087?tstart=0#19978087?tstart=0

**_10.7+_** You need to allow Agent-less logins with the following at a command line on the server:
```
 sudo defaults write /Library/Preferences/com.apple.servermgrd requireUserAgent -bool false
 sudo killall servermgrd
```

**_Ubuntu 12.04 Error:_** There is currently an upstream bug in openssl which may result in the following error
```
ERROR: Problem Contacting Server:<urlopen error [Errno 8] _ssl.c:504: EOF occurred in violation of protocol>
```
The bug is being tracked at https://bugs.launchpad.net/ubuntu/+source/openssl/+bug/965371

**_0.6.4 Upgrade:_** Note there is a new config file in the package which adds some extra options. So if you have used the old one in the past you'll need to copy them in there. _10.7_ support should be functional now. Please log bugs if you have any trouble.

**_0.6.2 -> 0.6.3 Upgrade:_** Simply download the tarball and overwrite your check\_osx\_server executable wherever you have placed it. 0.6.3 is purely a bugfix release to correct a problem with the HDD threshold triggers.

**Monitor & Automatically Restart**
  * AFP
  * Calendar
  * DHCP
  * Directory Services
  * DNS
  * FTP
  * IP Filter
  * Jabber
  * Mail
  * MySQL
  * NAT
  * Netboot
  * NFS
  * Podcast
  * Print
  * Quicktime Streaming Server
  * RADIUS
  * SMB/CIFS
  * Software Update/Patches Required
  * Software Update Service
  * Teams
  * VPN
  * Web
  * Web Objects
  * XGrid
  * XSan (In work. If you have access and can help test please get in touch)

**Hardware Monitoring**
  * Percentage Of Aggregated CPU(s) & Cores Used
  * View The Overall Kilobits/Second Network Usage
  * View Percentage Of HDD Used On All Drives

**New Informational Modes**
  * List AFP Users Connected
  * List SMB Users Connected
  * Check Server Roles & Services Active
  * List All Updates Server Requires