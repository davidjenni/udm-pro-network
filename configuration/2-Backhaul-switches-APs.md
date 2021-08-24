# Backhaul physical network

Connecting the UDM via CAT5e cabling to managed switches and Unifi access points creates the
physical backbone of the network:

![backhaul topo](/assets/UDP-Pro-topo.png)

The cabling between UDM, switches and the APs are all part of the untagged, native LAN.
Since that portion of the network carries multiple VLANs, it's also referred to as *trunked connections*.

All managed Unifi switches and Unifi Access Points acquire their IP address via DHCP.

## Port Profiles

| Profile Name | Native Network | Tagged Networks |
| ------------ | -------------- | --------------- |
| OFF | none | none |
| mgmt-90 | mgmt-90 | none |
| mgmt | mgmt-90 | mgmt (90) |
| Wired-VLANs | mgmt-90 | home (10), guest (20), IoT (30), media (40), camera (50) |
| Wireless-AP-VLANs | mgmt-90 | home (10), guest (20), IoT (30), media (40) |

## Managed switches

### UDM-Pro

| Port | Link name | Profile Name |
| ---: | --------- | ------------ |
| 1 | to US-8-150W-garage | Wired-VLANs |
| 3 | to US-8-60W-office | Wired-VLANs |
| 8 | direct mgmt access (laptop) | mgmt |
| 9 | uplink to T-Mobile ISP | All |

All unused ports are configured with profile *OFF*

### US-8-150W-garage

| Port | Link name | Profile Name | PoE |
| ---: | --------- | ------------ | --- |
| 1 | AP-LivingRoom | Wireless-AP-VLANs | PoE/PoE+ |
| 2 | AP-Studio | Wireless-AP-VLANs | PoE/PoE+ |
| 3 | AP-garage | Wireless-AP-VLANs | 24V Passive |
| 9 | Uplink to UDM | Wired-VLANs | (SFP 1GbE) |
| 10| downlink to Netgear GS108 | All | (SFP 1GbE) |

All unused ports are configured with profile *OFF*

### US-8-60W-office

| Port | Link name | Profile Name | PoE |
| ---: | --------- | ------------ | --- |
| 1 | uplink to UDM | Wired-VLANs | none |
| 2 | mgmt-office | mgmt-90 | none |
| 3 | home-office | home (10) | none |

All unused ports are configured with profile *OFF*

## Access Points

### WiFi Networks

All APs connect via trunk (mgmt-90) as management LAN

| SSID | Network | WiFi bands | Sec Protocol | Extras |
| ---- | ------- | ---------- | ------------ | ------ |
| jenni.ap | home (10) | Both | WPA-2/WPA-3 | Hi Perf Devices |
| jenni.guest | guest (20) | Both | WPA-2/WPA-3 | L2 Isolation |
| jenni.IoT | IoT (30) | 2.4 GHz | WPA-2 | Multicast Enhancement |
| jenni.media | media (40) | Both | WPA-2/WPA-3 | Multicast Enhancement |
