# Tailscale on a GL.iNET microrouter running OpenWRT
I succeeded in getting a 'GL.iNet GL-AR300M16-Ext' to route all traffic via an existing tailscale exit node.
This does currently not work when the router is in wifi repeater mode. Use wired phone tethering or wired LAN.
I used a GL-A300M16 because the "Mango" variant (N300) does not have sufficient space in ROM, [although there are ways around that.](https://blog.patshead.com/2020/10/tailscale-on-my-gl-dot-inet-mango-openwrt-router.html)

EDIT: There now is native support for selected models (see [docs](https://docs.gl-inet.com/router/en/4/tutorials/tailscale/) )

## configure an exit node
[according to the Tailscale documentation](https://tailscale.com/kb/1103/exit-nodes)

## Set up your router
using your preferred wifi credentials, internet connection, etc.

## Install tailscale on your router
[using this repo](https://github.com/adyanth/openwrt-tailscale-enabler). Use wget to download the release
```
wget https://github.com/adyanth/openwrt-tailscale-enabler/releases/download/{RELEASE}/openwrt-tailscale-enabler-{RELEASE}.tgz
```
_see [releases page](https://github.com/adyanth/openwrt-tailscale-enabler/releases) for the latest package._

## Connect to tailscale
log in to the router via ssh
```
ssh -oHostKeyAlgorithms=+ssh-rsa root@{ROUTERIP}
```
connect to tailscale (once again) with these arguments
```
tailscale up --reset --exit-node={EXITNODEIP} --accept-dns=false --exit-node-allow-lan-access
```
## Install LuCI
via GL.iNET admin panel (MORE SETTINGS -> Advanced -> Install)

## Add tailscale0 as an interface in LuCI
Network -> Interfaces -> Add -> Unmanaged -> select 'tailscale0'

## Add interface to WAN Firewall Group in LuCI
Network -> Interfaces -> Edit {NAMEOFTAILSCALEINTERFACE} -> Firewall Settings -> select 'WAN'

_Big thanks to [Pat Regan](https://twitter.com/patsheadcom) for [figuring this out](https://blog.patshead.com/2022/09/the-openwrt-routers-from-glinet-are-even-cooler-than-i-thought.html)._

There is a different approch [described here](https://www.freeshell.de/~pcfreak/mdwiki/#!content/linux-generic/openwrt-with-openvpn/openwrt-mit-openvpn.md) where a dedicated firewall zone is created to route all traffic through OpenVPN, but I didn´t have any luck implementing the same principle with Tailscale.


## TO DO
Set up guest wifi to use WAN only (without access to tailscale). Ideas welcome :)

There is the possibility to [have devices from the subnet local to the GL.iNET respond to requests from the subnet router´s subnet](https://forum.tailscale.com/t/is-subnet-routers-one-way/1051), although [static routes will have to be set](https://old.reddit.com/r/Tailscale/comments/xdtszi/subnet_routing_on_openwrt/iogmoxw/) and I did not explore this option.
