# EPFL VPN NM split-tunnel config

## How-to?

> [!NOTE]
> No, you can not use NetworkManager's “import” button, because AFAIK, it is not possible (or at least, annoying) to export custom routes.

1. Install the OpenConnect package and its NetworkManager integration (e.g., [`paru -S networkmanager-openconnect`](https://archlinux.org/packages/extra/x86_64/networkmanager-openconnect/).)
2. Copy the `epfl.nmconnection` to `/etc/NetworkManager/system-connections/epfl.nmconnection`
3. Give appropriate permissions (you'll need `sudo`):
   ```console
   # chmod 600 /etc/NetworkManager/system-connections/epfl.nmconnection
   # chown root:root /etc/NetworkManager/system-connections/epfl.nmconnection
   ```
4. Restart NetworkManager using your service manager, e.g., for systemd:
   ```console
   # systemctl restart NetworkManager
   ```
5. Connect to the new VPN connection named “epfl”

> [!WARNING]
> If you have another named network called “epfl”, feel free to change the `id` field in the `.nmconnection`

> [!WARNING]
> Your DNS requests may be still be routed through EPFL's DNS if your local resolver does not support Split DNS (e.g., `/etc/resolv.conf`.) You might want to consider `systemd-resolved` and configure it with NetworkManager (`man 5 NetworkManager.conf`).

And voilà! The EPFL traffic is routed through the VPN, and the rest (e.g., Steam, Discord) through your own connection. Make a speedtest to assert that :P 

## Why?

- Because I'm too stupid remembering exactly which fields to populate.
- Screen-sharing on Discord is pretty painful on the EPFL network.
  Connecting to the EPFL VPN yields pretty poor screen-sharing experience.
  This repo is a way for me to remember the required fields and routes to avoid that :')
- “No, I don't want my whole internet connections passing through EPFL just to SSH into a server!”

## Make it by myself?

- **DNS servers**: simply `dig dns{1,2}.epfl.ch {A,AAAA}`. Populate the `dns` fields.
- **Routes**: Populate the `route{1…n}` fields.
   - For the main EPFL network, I simply looked [here](https://www.epfl.ch/campus/services/en/it-services/network-services/wired-network/access-to-the-wired-network/11527-2/).
   - For the internal private network (`10.0.0.0/8`), what OpenConnect configures automatically is just a small part of the internal subnet (e.g., `10.250.0.0/16`). Therefore, it is required to have that “extra” route to access specific services, e.g., `sesame.epfl.ch`.

IPv6 does not work on my end (probably a shitty configuration on my end), but hey, IPv4 works -- and this is an IPv4 household here.
