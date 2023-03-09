# Firewall

## Groups

To make the firewall rules easier to read and manage, set up the following groups in
[Settings | Security | Internet Threat Management | Firewall ](https://10.0.10.1/network/default/settings/security/threat-management)

under *Groups*, enter the following:

| Name | Type | Values |
| ---- | ---- | ------ |
| All local LANS | IPv4 Address/Subnet | 10.0.10.0/24, 10.0.20.0/24 10.0.30.0/24, 10.0.30.0/24, 10.0.50.0/24 |
| Home Devices (low trust) | IPv4 Address/Subnet | 10.0.30.0/24, 10.0.530.0/24 |
| Local DNS | IPv4 Address/Subnet | 10.0.90.42 |
| Low Trust LANs | IPv4 Address/Subnet | 10.0.20.0/24, 10.0.30.0/24, 10.0.40.0/24, 10.0.50.0/24 |
| RFC1918 | IPv4 Address/Subnet | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |
| Trusted LANs | IPv4 Address/Subnet | 10.0.10.0/24 |
| UDM in local LANs | IPv4 Address/Subnet | 10.0.10.1/24, 10.0.20.1/24, 10.0.30.1/24, 10.0.40.1/24, 10.0.50.1/24, 10.0.90.1/24 |
| media LAN | IPv4 Address/Subnet | 10.0.40.0/24 |
| NAS | IPv4 Address/Subnet | 10.0.10.xx |
| Sonos devices | IPv4 Address/Subnet | 10.0.40.xx |

And some port groups:

| Name | Type | Values |
| ---- | ---- | ------ |
| DHCP | Port Group | 67 |
| DNS | Port Group | 53 |
| NTP | Port Group | 123 |
| UDM mgmt ports | Port Group | 22, 80, 443 |
| Plex TCP | Port Group | 32400, 32469 |
| Sonos appCtrl TCP | Port Group | 1400, 3400, 3401, 3500 |
| Sonos appCtrl UDP | Port Group | 1900, 1901, 6969 |

The resulting groups look like:

![Firewall groups](/assets/Settings-Security-Groups.png)

# Rules

**NOTE**: Before adding rules, make sure you do have a UDM-Pro backup! Any mistakes or misconfiguration can
lead to a lock out, where your PC/laptop can no longer reach the UDM-Pro!

By default, the UDM-Pro has full inter-VLAN communications enabled. Firewall rules are evaluated in order,
i.e. once an earlier allow or block rule is matched, the remaining rules are skipped. Best practice is to
list allow rules with concise match criteria first, followed by block rules that block whatever wasn't matched
before.

Most rules are for *LANin* (i.e. traffic from the LAN segment into the router/gateway),
with a few for *LANlocal*, i.e. traffic within the LAN segment)

![Firewall rules](/assets/Settings-Security-Rules.png)

## Rules for *LAN IN*:

| Index | Type | Description | Action | IPv4 Protocol | Source | Destination | Advanced |
| ----- | ---- | ----------- | ------ | ------------- | ------ | ----------- | -------- |
| 2000 | LANin | allow established /related | Accept | All | Any | Any | Match Established, Related |
| 2001 | LANin | allow all local to NTP | Accept | UDP | All local LANs | Any | .. |
| 2002 | LANin | allow Local DNS for all LANs | Accept | TCP, UDP | All local LANs | Group: Local DNS; Port: DNS | .. |
| 2003 | LANin | allow ping to Local DNS | Accept | ICMP | All local LANs | Local DNS | .. |
| 2004 | LANin | allow trusted to devices | Accept | All | Trusted LANs | Home Devices (low trust) | Enable logging |
| 2005 | LANin | Allow DNS admin from Trusted LANs | Accept | All | Trusted LANs | Group: Local DNS; Port: UDM mgmt ports |
| 2009 | LANin | block inter-LAN | Drop | All | RFC 1918 | RFC1918 | Enable logging |

## Rules for *LAN LOCAL*:

| Index | Type | Description | Action | IPv4 Protocol | Source | Destination | Advanced |
| ----- | ---- | ----------- | ------ | ------------- | ------ | ----------- | -------- |
| 2000 | LANlocal | drop invalid state | Drop | All | Any | Any | Match Invalid |
| 2001 | LANlocal | Block UDM from low trust LANs | Drop | All | Low Trust LANs | Group: UDM in local LANs; Port: UDM mgmt ports | Enable logging |

## Additional rules to accomodate media apps

The above rules are too strict and won't allow media devices to be discoverable and usable.
We need targeted firewall exceptions to accomodate those communications between the 'media' and 'home' VLANs,
e.g. to allow the Plex app in a smart TV to serve movies, music from a NAS.

### Plex

[General info on which ports are used by Plex](https://support.plex.tv/articles/201543147-what-network-ports-do-i-need-to-allow-through-my-firewall/)

| Index | Type | Description | Action | IPv4 Protocol | Source | Destination | Advanced |
| ----- | ---- | ----------- | ------ | ------------- | ------ | ----------- | -------- |
| 2006 | LANin | media to NAS | Accept | TCP | media LAN | Group: NAS; Port: Plex TCP | |

### Sonos speakers and app

[General info for Sonos firewall ports](https://support.sonos.com/s/article/688?language=en_US)

| Index | Type | Description | Action | IPv4 Protocol | Source | Destination | Advanced |
| ----- | ---- | ----------- | ------ | ------------- | ------ | ----------- | -------- |
| 2007 | LANin | Sonos appCtrl TCP | Accept | TCP | Trusted LANs | Group: Sonos devices; Port: Sonos appCtrl TCP | |
| 2008 | LANin | Sonos appCtrl UDP | Accept | UDP | Trusted LANs | Group: Sonos devices; Port: Sonos appCtrl UDP | |
