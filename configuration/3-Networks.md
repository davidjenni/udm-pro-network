# Networks

Time to create the networks and VLANs under [Networks](https://10.0.10.1/network/default/settings/networks):

| network name | CIDR range | VLAN id |
| ------------ | ---------- | ------- |
| home | 10.0.10.0/24 | 10 |
| guest | 10.0.20.0/24 | 20 |
| iot | 10.0.30.0/24 | 30 |
| media | 10.0.40.0/24 | 40 |
| camera | 10.0.50.0/24 | 50 |

Personal preference on the IP range starting IP address: For most VLANs, I prefer to reserve the
first ~30-50 IP addresses.

![home network settings](/assets/Settings-Networks-home.png)

Here's the resulting list of networks:

![all network settings](/assets/Settings-Networks.png)
