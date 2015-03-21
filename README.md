DD-WRT Adblock Plus to Privoxy
==============================

Download and convert Adblock Plus Filters to Privoxy Action- and Filterfiles
for DD-WRT Builds above 25000 and Kong's older Builds back to 23900 OLD

Based on the Original Script for OpenWRT by Andrwe:<br />
http://andrwe.org/scripting/bash/privoxy-blocklist

Modified to be used without Optware or any additional binaries. 

### Changelog

###### 151012-1

* Moved to Github
* Fixed some Syntax Errors (possibly - tested with shellcheck.net). Please update to the newest version

###### 141012-1

* Added Domain-Based Blocking
* Fixed Regex for whitelisting (now properly working)

##### 142711-1

* Initial Release
* Fixed Regex from original Script that would prevent the Filter and Actionlists from working
* Changed binaries that are used to be compatible with DD-WRT 25408 and similar

### Requirements:

* DD-WRT Build BS 25408+, Kong 23900+ or similar
* Privoxy compiled into firmware (/usr/sbin/privoxy)
* JFFS Partition - http://www.dd-wrt.com/wiki/index.php/Journalling_Flash_File_System

### Install Instructions:

1. Start by creating the initial folders to hold the Privoxy files. You'll need to access your router via telnet/SSH. The quickest way to get setup is copy the default Privoxy configuration files provided in the DD-WRT firmware from `/etc/privoxy`

```
mkdir -p /jffs/etc/privoxy
mkdir -p /jffs/tmp
cp -r /etc/privoxy/* /jffs/etc/privoxy
cd /jffs
```

Grab the latest version of `privoxy-blocklist.sh`, the easiest way to do this is using curl

`curl -k -O https://raw.githubusercontent.com/jamesmacwhite/ddwrt-adbp-to-privoxy/master/privoxy-blocklist.sh`

** Due to the wget package not being compiled with SSL support in DD-WRT, you will be unable to wget files under a domain using https

2. `chmod +x privoxy-blocklist.sh`

3. `sh privoxy-blocklist.sh`

4. Enable Privoxy in the DD-WRT web interface under `Services -> Adblocking` by setting the radio button to Enable. In addition you will need to enable `Custom Configuration`. For convenience you may also want to use `Transparent Mode`. This mode will create a iptables rule that redirects all traffic on port 80 through Privoxy. Doing this means you don't need to configure each client to point to the proxy via [x.x.x.x]:8118, all traffic will be redirected automatically.

5. Change `CONFDIR` to the place where you store your filterlists if you didn't use the default path - Default: `/jffs/etc/privoxy`

6. Add all Filter and Actionlists to your Custom Configuration like this
(for each AdblockPlus-List one Filter- and one Actionlist will be generated):

<img src="http://abload.de/img/filterlistsexeqj.jpg" alt="DD-WRT Privoxy web interface configuration page" width="50%" />

7. Click Save - then Apply Settings

Wait a bit (approx. 5 minutes) - now Privoxy should be restarted including your AdblockPlus Filters and Actions. You can verify this under [http://config.privoxy.org/show-status] (you can only access this page if you're using Privoxy).

It should look similar to this:

<img src="http://abload.de/img/privoxy-config-succesf6udi.jpg" alt="Privoxy Config Page" width="50%" />

### Configuration (in privoxy-blocklist.sh):

Modify the following vars to your needs:

`CONFDIR=/jffs/etc/privoxy` - Path to Privoxy configuration folder

`TMPDIR=/jffs/tmp/privoxy-blocklist` - Path to the temp folder for building the blocklists
```
ADBLOCKLISTS=" \
https://easylist-downloads.adblockplus.org/easylist.txt \
https://easylist-downloads.adblockplus.org/antiadblockfilters.txt \
https://easylist-downloads.adblockplus.org/malwaredomains_full.txt"
```
Add additional Adblock lists using a backslash to denote a new line

### Usage:

`sh privoxy-blocklist.sh` - every time you want to update the lists (see install instructions above)
