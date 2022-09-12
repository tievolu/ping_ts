# ping_ts
Simple tool to send and receive timestamped pings (ICMP type 13), implemented in perl.

This tool can be used to measure upload and download latency separately.

Designed to work on OpenWrt. May work on other platforms if the necessary perl modules are installed.

## Instructions

##### Download
```
wget https://raw.githubusercontent.com/tievolu/ping_ts/main/ping_ts
```

##### Install required modules:
```
opkg update; opkg install perl perlbase-posix perlbase-socket perlbase-time
```

##### Usage
```
Usage
  ping_ts [options] <destination>

Options
  <destination>      Hostname or IP address
  -c <count>         Stop after <count> pings
  -i <interval>      Seconds between ping requests
  -r                 Print raw timestamps (seconds since the epoch)
  -f                 Attempt to fix ICMP timestamps
```

## Example output
```
# ./ping_ts bbc.co.uk
[Mon Sep 12 15:44:16.899 2022] target=151.101.128.81 id=13342 seq=1 orig=53056872 recv=53056885 tran=53056885 end=53056899 tx=13 rx=14 rtt=27
[Mon Sep 12 15:44:17.916 2022] target=151.101.128.81 id=13342 seq=2 orig=53057873 recv=53057903 tran=53057903 end=53057916 tx=30 rx=13 rtt=43
[Mon Sep 12 15:44:18.894 2022] target=151.101.128.81 id=13342 seq=3 orig=53058875 recv=53058880 tran=53058880 end=53058893 tx=5 rx=13 rtt=18
[Mon Sep 12 15:44:19.896 2022] target=151.101.128.81 id=13342 seq=4 orig=53059877 recv=53059882 tran=53059882 end=53059896 tx=5 rx=14 rtt=19
[Mon Sep 12 15:44:20.898 2022] target=151.101.128.81 id=13342 seq=5 orig=53060878 recv=53060884 tran=53060884 end=53060898 tx=6 rx=14 rtt=20
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

Running with the `-f` option will attempt to "fix" the ICMP timestamps to produce sensible absolute values. For example:

```
# ./ping_ts -r -f 195.27.1.1
[1662998163.338] target=195.27.1.1 id=20265 seq=1 orig=57363318 recv=57363328 tran=57363328 end=57363338 tx=10 rx=10 rtt=20 offset=2648
[1662998164.340] target=195.27.1.1 id=20265 seq=2 orig=57364319 recv=57364330 tran=57364330 end=57364340 tx=11 rx=10 rtt=21 offset=2648
[1662998165.345] target=195.27.1.1 id=20265 seq=3 orig=57365320 recv=57365335 tran=57365335 end=57365345 tx=15 rx=10 rtt=25 offset=2648
[1662998166.341] target=195.27.1.1 id=20265 seq=4 orig=57366321 recv=57366331 tran=57366331 end=57366341 tx=10 rx=10 rtt=20 offset=2647
[1662998167.343] target=195.27.1.1 id=20265 seq=5 orig=57367323 recv=57367332 tran=57367332 end=57367342 tx=9 rx=10 rtt=19 offset=2648
```
```
offset  =  Continually updated and applied to ensure that tx and rx are never less than half the lowest recorded rtt
```
