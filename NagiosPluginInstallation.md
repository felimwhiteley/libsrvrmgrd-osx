# Prerequisites #

  * Nagios server set up and running.
  * Python [plist-library](http://svn.python.org/projects/python/trunk/Lib/plistlib.py)
  * The **check\_osx\_server** & **osx\_server\_event\_handlers**
  * The **srvrmgrdIO.py** module installed in a system wide Python path or in same directory as the plugins
  * The plugin config files updated to include the correct path

# Installation #

## plistlib ##

This can be downloaded directly from [http://svn.python.org/projects/python/trunk/Lib/plistlib.py](http://svn.python.org/projects/python/trunk/Lib/plistlib.py), it may be available on Redhat/Fedora via yum but it is available on Debian/Ubuntu by:
```
sudo aptitude install python-plistlib
```

If it is not available system wide then copy the plistlib.py file into the same directory you have stored the plugins (possibly /usr/lib/nagios/plugins/).

## Plugin Installation ##

This is a simple case of copying them to the correct plugin location where the rest of the Nagios plugins are stored on your distribution (or OS). On a Debian/Ubuntu system it is:
```
/usr/lib/nagios/plugins/
```

You will then need to update the osx\_server.cfg file included in the tarball and modify the values **NAGIOS\_PLUGIN\_PATH** and **NAGIOS\_EVENT\_HANDLER\_PATH** to the correct path. It should then be merged into your master config or added into the correct config search path. On Ubuntu copying it to:
```
/etc/nagios-plugins/config/
```
Was enough for it to be picked up by Nagios.

**_NOTE:_** If your event handler location is different from the plugin path and you have not installed **plistlib.py** and **srvrmgrdIO.py** system wide then you will need to copy those files in with the event handler also.

## libServerManagerDaemon.ini ##
This is an optional step but if you want to change some of the default behavoir this file needs to be copied to
```
/etc/
```
and can be edited with a text editor. The initial values of OFF (in this case 0) are what the plugin will assume if no such config file exists.

## Configuring A Service ##
Assuming we have a server setup already called **_server.domain.lcl_** then monitoring **Apple File Share Protocol** is possible by with:
```
define service{
,              host_name              server.domain.lcl
               service_description    OS X AFP
               check_command          check_osx_afp!311!username!password
               use                    generic-service
}
```
You might need to modify this slightly depending on your Nagios configuration, this was from an Ubuntu installation. Points to note are you only need to supply 3 values
  * Port the server is listening on (generally 311 unless you are using [PAT](https://secure.wikimedia.org/wikipedia/en/wiki/Port_address_translation))
  * Admin username on the server
  * That admin users password

Note you could use Nagios $USERx$ variables if you have enough free or have a common account across servers.

## Sub-Service Alerts ##

Some services come with many sub-services which can be off/failed while the master service is fine. For instance the **QTSS** or Quicktime Streaming Server, has _AFP_, _DHCP_, _HTTP_, _NFS_, _TFTP_ sub-services. If any of these is in a stopped state we will be alerted. This might not always be desirable. Simply running the **_check\_osx\_server_** command with no arguments will output each service and it's corresponding sub-services. If you add and extra comma seperated list in your config you can switch off the check on those sub-services. For example:
```
check_osx_server jabber 172.16.12.1 311 srvadmin srvpassword mucState,proxyState
```
mucState & proxyState will be ignored and will not trigger a warning if they are stopped. In the config setup that would be:
```
define service{
,              host_name              server.domain.lcl
               service_description    OS X Jabber
               check_command          check_osx_jabber!311!srvadmin!srvpassword!mucState,proxyState!
               use                    generic-service
}
```

# Troubleshooting #

## 401 Error ##
  * The user you are trying to access the server is not member of the admin group
  * You have run the plugin from the command line already, data files are stored in /tmp which you should remove as Nagios runs under it's own account and can't over write them
## 404 Error ##
  * This is the result of incorrect user password or username accessing the server
## (null) Output ##
  * Run the plugin from the command line, it's likely a missing python dependancy
  * This also seems to occur ([issue 9](https://code.google.com/p/libsrvrmgrd-osx/issues/detail?id=9)) when running Nagios on a Apple Server. To avoid this it should be run from launchd rather than the command line