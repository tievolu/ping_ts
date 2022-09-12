# ping_ts
Simple tool to send and receive timestamped pings (ICMP type 13), implemented in perl.

Designed to work on OpenWrt.

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
Mon Sep 12 14:59:08 2022: target=151.101.128.81 id=8029 seq=1 orig=50348755 recv=50348762 tran=50348762 end=50348776 tx=7 rx=14 rtt=21
Mon Sep 12 14:59:09 2022: target=151.101.128.81 id=8029 seq=2 orig=50349778 recv=50349782 tran=50349782 end=50349797 tx=4 rx=15 rtt=19
Mon Sep 12 14:59:10 2022: target=151.101.128.81 id=8029 seq=3 orig=50350798 recv=50350805 tran=50350805 end=50350820 tx=7 rx=15 rtt=22
Mon Sep 12 14:59:11 2022: target=151.101.128.81 id=8029 seq=4 orig=50351821 recv=50351826 tran=50351826 end=50351840 tx=5 rx=14 rtt=19
Mon Sep 12 14:59:12 2022: target=151.101.128.81 id=8029 seq=5 orig=50352842 recv=50352846 tran=50352846 end=50352860 tx=4 rx=14 rtt=18
Mon Sep 12 14:59:13 2022: target=151.101.128.81 id=8029 seq=6 orig=50353862 recv=50353867 tran=50353867 end=50353882 tx=5 rx=15 rtt=20
```
