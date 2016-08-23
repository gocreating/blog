title: Packet Sniffer Quick Note
date: 2015-05-07 11:40:01
categories:
  - Uncategorized
---
　
<!-- more -->

## Setup Monitor Mode[^1][^2]

### In Linux

```
ifconfig wlan0 down
iwconfig wlan0 mode Monitor
ifconfig wlan0 up
```

### In Mac

```
sudo ln -s /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport /usr/sbin/airport
sudo airport -z #disassociates you card from the current network
sudo airport -c["channel you want to switch to"] #sets the channel the card monitors on
```

[^1]: <http://blog.hexacta.com/how-to-listen-to-wifi-with-node-js/>
[^2]: <https://wiki.wireshark.org/CaptureSetup/WLAN#Mac_OS_X>