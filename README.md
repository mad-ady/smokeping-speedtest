# smokeping-speedtest
Smokeping::probes::speedtest

Integrates speedtest-cli (https://github.com/sivel/speedtest-cli) as a probe into smokeping. The variable "binary" must
point to your copy of the speedtest-cli program. If it is not installed on
your system yet, you should install the latest version from https://github.com/sivel/speedtest-cli.

The Probe asks for the given resource one time, ignoring the pings config variable (because pings can't be lower than 3).

You can ask for a specific server (via the server parameter) and record a specific output (via the measurement parameter).

Parameters:
* binary => The location of your speedtest-cli binary.
* server => The server id you want to test against (optional). If unspecified, speedtest.net will select the closest server to you. The value has to be an id reported by the command speedtest-cli --list
* measurement => What output do you want graphed? Supported values are: ping, download, upload

Logging:
You can get logs of what goes on inside the plugin either by running smokeping with --debug, or by changing this line:
```
  #set to LOG_ERROR to disable debugging
  setlogmask(LOG_MASK(LOG_ERROR));
```
  
  to
  
```
  #set to LOG_ERROR to disable debugging
  setlogmask(LOG_MASK(LOG_DEBUG));
```
  
After doing this (and restarting smokeping), the plugin's logs will go to syslog, local0.debug. You will need something like 
```
  local0.*     /var/log/local.log
```
in your syslog configuration.


Example probe configuration (poll every hour):
```
+ speedtest
binary = /usr/local/bin/speedtest-cli
timeout = 300
step = 3600
offset = random
pings = 3

++ speedtest-download
measurement = download

++ speedtest-upload
measurement = upload

++++ download_from_NextGen_Communications
menu = download_from_NextGen_Communications
title = download_from_NextGen_Communications
probe = speedtest-download
server = 1737
measurement = download
host = dummy.com

++++ upload_to_NextGen_Communications
menu = upload_to_NextGen_Communications
title = upload_to_NextGen_Communications
probe = speedtest-upload
server = 1737
measurement = upload
host = dummy.com
```

Bugs: 
* Currently the probe's unit of measurement is hardcoded to "bps". This will be a problem if you want to measure "ping", which is reported in seconds.
* For large values the overview graphs will have wrong data. You need to add something like this to your overview section:
```
+overview
max_rtt = 1000000000
```

