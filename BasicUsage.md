# Introduction #

This code is fully working, so far it's been tested on Panther, Tiger and Leopard (Server Version). It doesn't work on Client versions as they do not have the underlying Server Manager Daemon running.

To see the original inspiration for this library written by Andre LaBranche and some more examples based on the old version please visit [Andre's Blog](http://www.dreness.com/blog/?p=38)


## Why Store Data In /tmp ? ##

The reason for this is it allows multiple applications to make use of the data. If you look at some of the data that Server Manager outputs there is a **_LOT_** of useful info in one data stream, some of it relevant to many forms of query. Rather than have one massive python script that attempts to do many checks it was better (in my opinion) to allow a tempfile which can be queried by multiple applications. The srvrmgrdIO library will refresh it every 5mins so allowing the data to remain fresh while also minimizing network queries.

When data is stored it will be of the form:
```
/tmp/[server Name Or IP Addr]_[Port Server Listens On]_[ServerManager Module]_[Request Type].dat
```

An Example file based on the below piece of python code would result in:
```
/tmp/10.10.139.253_311_servermgr_nfs_getState.dat
```

The file is a Python pickle file, stored in binary form which allows Python to maintain data structures etc.

## Example - Getting NFS Status ##

```
import srvrmgrdIO
import pickle

server = sys.argv[1]
port = sys.argv[2]
user = sys.argv[3]
password = sys.argv[4]

request = srvrmgrdIO.buildXML('getState', 'withDetails', '')
servermgrdModule = 'servermgr_nfs'

ServerDataFile = srvrmgrdIO.buildDataFile(servermgrdModule, request, server, port, user, password)

DataFile = open(ServerDataFile, "rb")
DataDump = pickle.load(DataFile)
DataFile.close()

if `DataDump['state']` == "'RUNNING'" :
        service_status = DataDump['state']
        print service_status
else :
        print "NFS Is Not Running"

```

The best way to discover the functioning of Server Manager itself is to point a browser to https://your_server:311/

This will allow you to find out the required module names which form the **request** variable and what module they belong to which corresponds to the **servermgrdModule** variable.