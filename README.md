# Media Vision Metadata API — public releases

This repository publishes **compiled native application and Proxmox installer
assets only**. Source code is maintained separately in a private repository.
No IMDb datasets, indexes, API credentials or live configuration are published.

## One-line native Proxmox installer

Run this command **as root on a Proxmox VE host**, to create a brand-new
Debian 13 LXC with nesting enabled (never to overwrite an existing VMID):

    bash -c "$(curl -fsSL https://github.com/zemerdon/media-vision-api-releases/releases/download/v0.1.1/bootstrap-native.sh)"

The blue visual wizard has the same options as the Media Vision installer:
Default Install, Advanced Install, User Defaults and Settings. It supports
active storage selection, Debian 13 template, VMID, network bridge,
DHCP or static IP, hostname, disk, CPU and RAM. Its defaults include
20 GiB disk, 4 GiB RAM and two cores.

## Ports

| Service | URL |
| --- | --- |
| Metadata API + health | http://LXC-IP:9292 |
| Passwordless Web UI | http://LXC-IP:9393/ |

API requests use port 9292. The key-management Web UI opens directly at
port 9393, with **no /admin suffix**. It can create named client keys and
rotate/revoke them. The two port surfaces reject each other's routes.

**Warning: The admin UI has NO PASSWORD or sign-in. Anyone who can reach port
9393 can manage/revoke all API keys. It is NOT safe to expose unprotected to
the public internet.** The installer does not create NAT or firewall rules,
and does not guarantee external reachability. Metadata requests themselves
continue to require API keys.

The service starts without an IMDb database. No initial IMDb ingestion,
automatic refresh or dataset updates run during installation. Metadata
lookups will not work until an index is separately populated safely.
First-boot on real Proxmox and updater/rollback have not yet been tested.

[Media Vision application releases](https://github.com/zemerdon/media-vision/releases)
are independent of these Metadata API releases.

[View the MV API v0.1.1 release](https://github.com/zemerdon/media-vision-api-releases/releases/tag/v0.1.1)

## Failed first-install diagnostics

The installer records guest installation output in /root/mv-api-install.log.
If guest setup fails after creation, the LXC and verified software are
retained for diagnosis. From Proxmox, inspect using:
    pct exec VMID -- tail -n 100 /root/mv-api-install.log
Use an unused VMID for a fresh install. The installer never overwrites or
automatically destroys an existing LXC.
