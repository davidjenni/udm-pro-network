# Wifi networks

To create/configure Wifi networks, goto [Wifi](https://10.0.10.1/network/default/settings/wifi)

The most relevant setting is to set the name, e.g. ```jenni.ap``` and select the corresponding
network, here: ```home```

![Settings Wifi home / jenni.ap](/assets/Settings-Wifi-home.png)

Under the *Advanced* tab, accept most of the defaults, except for these:

| Wifi name | Setting | Value |
| --------- | ------- | ----- |
| *.ap | High Performance Devices | ON |
| *.guest | L2 Isolation | ON |
| *.IoT | Wifi Band | 2.4 GHz only (since most IoT have no 5 GHz support) |
| *.media | Multicast Enhancement | ON |
