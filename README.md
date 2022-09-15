# ping_ts
Simple tool to send and receive timestamped pings (ICMP type 13), implemented in perl.

This tool can be used to measure upload and download latency separately.

Designed to work on OpenWrt. May work on other platforms if the necessary perl modules are installed.

## Instructions

##### Download
```
wget https://raw.githubusercontent.com/tievolu/ping_ts/main/ping_ts
```

##### Set execute permission
```
chmod 755 ping_ts
```

##### Install required modules:
```
opkg update; opkg install perl perlbase-filehandle perlbase-time perl-threads
```

##### Usage
```
Usage
  ping_ts [options] <destination>

Options
  <destination>      Hostname or IP address
  -c <count>         Stop after <count> pings
  -i <interval>      Seconds between ping requests
  -t <timeout>       Seconds to wait for each response
  -r                 Use raw ICMP timestamp values - do not attempt to correct them
  -u                 Print log timestamps in unix time (seconds since the epoch)
```

## Example output
```
# ./ping_ts -c 5 bbc.co.uk
[Thu Sep 15 16:54:00.375 2022] dest=151.101.0.81 id=27586 seq=1 orig=57240356 recv=57240365 tran=57240365 end=57240375 tx=9 rx=10 rtt=19 offset=2
[Thu Sep 15 16:54:01.376 2022] dest=151.101.0.81 id=27586 seq=2 orig=57241357 recv=57241366 tran=57241366 end=57241376 tx=9 rx=10 rtt=19 offset=2
[Thu Sep 15 16:54:02.379 2022] dest=151.101.0.81 id=27586 seq=3 orig=57242358 recv=57242368 tran=57242368 end=57242378 tx=10 rx=10 rtt=20 offset=2
[Thu Sep 15 16:54:03.379 2022] dest=151.101.0.81 id=27586 seq=4 orig=57243359 recv=57243369 tran=57243369 end=57243379 tx=10 rx=10 rtt=20 offset=2
[Thu Sep 15 16:54:04.378 2022] dest=151.101.0.81 id=27586 seq=5 orig=57244360 recv=57244369 tran=57244369 end=57244378 tx=9 rx=9 rtt=18 offset=3

--- 151.101.0.81 timestamped ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
Round-trip    min/avg/max = 2/19.200/20 ms
Request  (TX) min/avg/max = 2/9.400/10 ms
Response (RX) min/avg/max = 2/9.800/10 ms
```

```
orig  =  time at which ICMP request was sent (milliseconds since midnight UTC)
recv  =  time at which ICMP request was received (milliseconds since midnight UTC)
tran  =  time at which ICMP response was sent (milliseconds since midnight UTC)
end   =  time at which ICMP response was sent (milliseconds since midnight UTC)
tx    =  recv - orig  =  request transit time i.e. upload latency (milliseconds)
rx    =  end - tran   =  response transit time i.e. download latency (milliseconds)
rtt   =  end - orig   =  round trip time (milliseconds)
```

## Timestamp correction
If the destination's clock is not _exactly_ synchronised to the local clock, or the destination's timestamps do not count from midnight UTC, the results will be misleading in absolute terms, but may still be useful in relative terms.

By default, ping_ts will attempt to "fix" the ICMP timestamps to produce sensible absolute values. For example:

##### With correction
```
# ./ping_ts -u 195.27.1.1
[1663257284.685] dest=195.27.1.1 id=27665 seq=1 orig=57284668 recv=57284676 tran=57284676 end=57284685 tx=8 rx=9 rtt=17 offset=2566
[1663257285.689] dest=195.27.1.1 id=27665 seq=2 orig=57285669 recv=57285680 tran=57285680 end=57285689 tx=11 rx=9 rtt=20 offset=2566
[1663257286.687] dest=195.27.1.1 id=27665 seq=3 orig=57286670 recv=57286678 tran=57286678 end=57286686 tx=8 rx=8 rtt=16 offset=2567
[1663257287.694] dest=195.27.1.1 id=27665 seq=4 orig=57287672 recv=57287686 tran=57287686 end=57287694 tx=14 rx=8 rtt=22 offset=2567
[1663257288.695] dest=195.27.1.1 id=27665 seq=5 orig=57288673 recv=57288687 tran=57288687 end=57288695 tx=14 rx=8 rtt=22 offset=2567
```
```
offset  =  Value applied to tx and rx to ensure that they are never less than half the lowest recorded rtt
```
##### Without correction
```
root@beccacheese:/etc/scripts/util# ./ping_ts -u -r 195.27.1.1
[1663257478.040] dest=195.27.1.1 id=27849 seq=1 orig=57478020 recv=57475466 tran=57475466 end=57478040 tx=-2554 rx=2574 rtt=20
[1663257479.046] dest=195.27.1.1 id=27849 seq=2 orig=57479022 recv=57476470 tran=57476470 end=57479046 tx=-2552 rx=2576 rtt=24
[1663257480.043] dest=195.27.1.1 id=27849 seq=3 orig=57480023 recv=57477468 tran=57477468 end=57480043 tx=-2555 rx=2575 rtt=20
[1663257481.044] dest=195.27.1.1 id=27849 seq=4 orig=57481024 recv=57478469 tran=57478469 end=57481044 tx=-2555 rx=2575 rtt=20
[1663257482.049] dest=195.27.1.1 id=27849 seq=5 orig=57482025 recv=57479474 tran=57479474 end=57482049 tx=-2551 rx=2575 rtt=24
```
The tool will also attempt to correct problems that occur when the local or destination timestamp counters get reset. This usually happens around midnight UTC, but if the destination's timer is based on something else it can happen at any time.
