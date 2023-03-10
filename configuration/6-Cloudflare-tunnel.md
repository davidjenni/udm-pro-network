# Cloudflare tunnel to LAN

A safe way to make LAN/home internal http nodes reachable from the internet,
using cloudflare's [Zero Trust Network Access](https://www.cloudflare.com/plans/zero-trust-services/).
The 'Free Plan' is sufficient for personal, non-business use.

The Cloudflare tunnel is a variation of a VPN. Since the tunnel connection is initiated from within the local network,
no DDNS setup nor opening of VPN ports is required at the local UDM gateway.

This setup assume you already own one or more registered internet domains.

## Create tunnel in cloudflare

- [Create/login to cloudflare dashboard](https://dash.cloudflare.com/login)
- add your existing domain name as "Website" to the CF dashboard (select the free plan for private usage). This imports the DNS settings from the registrar's DNS (but does leave the actual registration at the existing registrar). It's ok to have no A/CNAME etc records at CF.
- from your registrar's DNS management page, replace the nameserver entries with what CF's dashboard asks for, e.g. `alec.ns.cloudflare.com`
- wait until CF dashboard for the domain shows NS change has been validated (should also trigger an email from CF)

- from CF ZeroTrust dashboard, select "Access" | "Tunnels"; create a new tunnel
- from the next step, the "Install connector" step, select "Docker" and copy the docker run incantation.

## Docker host, e.g. using Synology NAS' docker host

- from Synology's "Package Manager", add "Docker" package, and launch/open that
- under "Registry", search the `cloudflared` docker image and download it
- under "Image", select & launch the `cloudfared` image; in the "Network" step, select "Use the same network as Docker Host" option
- under "General Settings":
  - set a container name, e.g. `<mydomain.name>-cloudflared`
  - check "Enable auto-restart"
  - select/click "Advanced Settings", then:
  - select "Execution Command" tab, and paste a cleaned-up version from CF's docker incantation into the "Command" field:
    only the `token run --token <tokenValue>` parts are needed; remove `docker run cloudflare/cloudflared:latest` and the `--no-autoupdate` parts
- hit "Next" from Advanced Settings and General Settings, and from "Volume Settings" as well (no volumes/files needed)

## Configure tunnels

With the cloudflared running in local docker, back to the CF ZeroTrust configuration. It should now show under "Connectors" the cloudflared as running, and with an IP address.

Time to add routes/tunnels to NAS, pi-hole, UDM etc.:
- under "Public hostnames" tab, click "Add a public hostname"
- specify subdomain name
- specify local service scheme and local url w/ port, e.g. `10.0.90.42`

For local `https:`, expand "Additional application settings" and update e.g. TLS settings to enable "No TLS Verify" etc.

## Securing remote access

- from CF ZeroTrust dashboard, select "Settings" | "Authentication"
- by default, there is Cloudflare's "One-time PIN". Add any additional authenticators, e.g. Github, AzureAD etc.

### Add AAD authentication

- select "Add New" and click "Add Azure AD". Follow its help screen and open a second window for <https://portal.azure.com> and in AAD, create & configure the app registration.
- once tenantid, appId, clientSecret are configured, return to the CF ZT dashboard, click "Test" in the list of "Login methods" under the "Authentication" setting
- ensure MFA is setup for the user accounts in AAD

### Configure App policy and authentication

- from CF ZeroTrust dashboard, select "Access" | "Applications", then "Add an application"
- select the "Self-hosted" type
- Application Configuration:
  - name: e.g. "tunnel-access-frontdoor", with e.g. session duration of "24h"
  - subdomain: `*` to have it apply to all tunnels
- add a default policy:
  - name e.g. "default-allow", Action = "Allow"
  - rules:
  - Include rule: Selector = "Emails" or "Emails ending" and enumerate allowed/accepted email addresses and email domains
  - under tab "Authentication", add/select the AzureAD (the default OneTime PIN can be deselected)

Login screen example: ![Cloudflare ZeroTrust Access portal](/assets/CF-ZeroTrust-Access-portal.png)

## References
- [Cloudflare tunnel docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/)
- [CrosstalkSolutions blog](https://www.crosstalksolutions.com/cloudflare-tunnel-easy-setup/)