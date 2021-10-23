# Setting up the UDM-Pro

## Setup wizard and assign IP address

Not going into too many details, there's good documentation on how to run through the UDM-Pro setup wizard:

- [Unifi Network - Getting Started](https://help.ui.com/hc/en-us/articles/360012192813)
- [Setup Network Application](https://help.ui.com/hc/en-us/articles/1500012237441-UniFi-Network-Use-the-UniFi-Network-Application)
- [CrossTalk Solutions - Initial Wizard Setup](https://youtu.be/beniNcXaAKQ?t=248)

To make the initial configurations, plug in your PC or laptop directly into one of the ports on the UDM-Pro;
configure your PC/laptop/MBP to acquire DHCP addresses. Open a browser at <http://192.168.1.1>

Even though in my network, I plan on using a 10/8 private network, I started running the initial setup of
UDP-Pro and adopting the Unifi devices from the UDM-P's default ```192.168.1.1``` address. Run through the
wizard to connect to your ISP, update the UDM-P and adopt any switches and APs you have.

Note that you need to initially login to UDM-Pro with a SSO login from UI.com: <https://account.ui.com/>

The setup here assumes a local DNS server, I am using a [Pi-hole DNS server](https://pi-hole.net/) on ```10.0.90.42```

Once you've logged in to your UDM-Pro using the SSO login, best practice is to add a local admin and set the following under:
```Portal | Advanced```:

![Portal-Advanced-Settings](/assets/Portal-Advanced-settings.png)

Enable SSH and set your strong SSH password (you do keep all your passwords in a password manager like 1Password, right?)
While here, also ensure that ```Remove Access`` is now disabled (unless you want to enable remote configuration of your site via UI's cloud).

Once the Unifi devices have been adopted, switch the UDM-Pro to your desired IP address, in my case:
```10.0.90.1``` by navigating to [Network App | Settings | Networks](https://10.0.90.1/network/default/settings/networks)
Change the **LAN** network to:

| Label | Value |
| ----- | ----- |
| Enable Network | on |
| Name | ```mgmt-90``` |
| Advanced |  |
| VLAN ID | leave empty |
| Device Isolation | off |
| IGMP Snooping | off |
| Auto Scale Network | off |
| DHCP Mode | DHCP Server |
| Gateway IP/Subnet | ```10.0.90.0/24``` |
| DHCP Range | 10.0.90.100 - 10.0.90.254 |
| DHCP Name Server | Manual
| | 10.9.0.42 |
| DHCP Lease Time | 7200 |
| DHCP Gateway IP | Auto |

Leave all the other settings on their default/OFF settings.

Apply/save those settings and reconnect to the UPM-Pro on the new IP address: <https://10.0.90.1>

# Enable mDNS

To better support e.g. Chromecast or Apple cast, enable multicast DNS to better connect to media devices:

Go to [Network App | Settings | Advanced Features | Advanced Gateway Settings | Multicast DNS](https://10.0.10.1/network/default/settings/advanced/gateway/mdns/form)

![enable mDNS](/assets/Settings-Advanced-mDNS.png)
