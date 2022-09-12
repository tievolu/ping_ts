# ping_ts
Simple tool to send and receive timestamped pings (ICMP type 13), implemented in perl.

Designed to work on OpenWrt. May work on other platforms if the necessary perl modules are installed.

To download:
```
wget https://raw.githubusercontent.com/tievolu/ping_ts/main/ping_ts
```

To install required modules:
```
opkg update; opkg install perl perlbase-posix perlbase-socket perlbase-time
```

Usage:
```
Usage
  ping_ts [options] <destination>

Options
  <destination>      Hostname or IP address
  -c <count>         Stop after <count> pings
  -i <interval>      Seconds between ping requests
```

Example output:
```
# ./ping_ts bbc.co.uk
[Mon Sep 12 15:44:16.899 2022] target=151.101.128.81 id=13342 seq=1 orig=53056872 recv=53056885 tran=53056885 end=53056899 tx=13 rx=14 rtt=27
[Mon Sep 12 15:44:17.916 2022] target=151.101.128.81 id=13342 seq=2 orig=53057873 recv=53057903 tran=53057903 end=53057916 tx=30 rx=13 rtt=43
[Mon Sep 12 15:44:18.894 2022] target=151.101.128.81 id=13342 seq=3 orig=53058875 recv=53058880 tran=53058880 end=53058893 tx=5 rx=13 rtt=18
[Mon Sep 12 15:44:19.896 2022] target=151.101.128.81 id=13342 seq=4 orig=53059877 recv=53059882 tran=53059882 end=53059896 tx=5 rx=14 rtt=19
[Mon Sep 12 15:44:20.898 2022] target=151.101.128.81 id=13342 seq=5 orig=53060878 recv=53060884 tran=53060884 end=53060898 tx=6 rx=14 rtt=20
```
