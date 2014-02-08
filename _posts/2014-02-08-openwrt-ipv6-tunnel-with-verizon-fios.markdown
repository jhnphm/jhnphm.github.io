---
layout: post 
title: "OpenWRT IPv6 Tunnel with Verizon FIOS" 
date: 2014-02-26 23:50:52 
categories: ipv6 openwrt verizon 
---

So I installed OpenWRT last week on a router a friend loaned me.
Verizon's router isn't particularly bad for what it does (specifically
the Rev I Actiontec), but it really lacks customizability, and IPv6 tunneling
support to correct Verizon's deficiencies. 

I was able to acquire an ASUS RT-N16 last weekend and install OpenWRT on it. The
[unofficial builds][rtn16_unofficial] linked from the [wiki][openwrt_rtn16]
didn't work for me, and the wiki probably hasn't been updated for the latest
available build, so I found the [snapshot build][openwrt_snapshot] for
bcrm47xx and installed it via TFTP. The web interface ([LUCI][luci]) was
installed and enabled. 

Since I'm using Verizon, using FIOs TV, and wanted the ASUS as the primary
router in order to use it as an IPV6 tunnel endpoint , I attempted to set up
double bridging (Option 5) as described in this [DSLReports posting][routercfg].

Unfortunately the MI424WR Rev I is incompatible with this configuration. I
attempted to use Option 2, but the double NAT breaks Steam. Finally I configured
a static route on the Actiontec to the ASUS router's subnet and disabled NAT and
the IPv4 firewall on the ASUS router.  This still breaks UPnP and NAT is
still being done on the Verizon router, but it's something I can live with. The
Rev I of the Verizon router isn't terrible hardware-wise, it's just incompatible
with custom firmware while retaining [MoCA][moca] support for the TV.


I signed up for a Hurricane Electric (HE) [tunnel][henet] and configured a tunnel.
IRC does not work until one takes the [IPv6 test][hetest] to reach Sage level.
As a bonus, they give you a T-shirt. 

As IPv6 addresses can be long and difficult to remember, I wanted to enable DDNS
for local computers in the network, which requires DHCPv6. By default this is
enabled in OpenWRT, and also sets a [Unique Local Address][wiki_ula] for the
interface. Both the ULA addresses and the global addresses from HE were assigned
via DHCPv6. This would otherwise be fine, however NetworkManager on Linux will
only pick up one of the assigned addresses, whichever gets sent last. To fix
this, the ULA address had to be cleared from the router configuration so that
only the GUA was assigned.

Android does not support DHCPv6 but will still work w/ [SLAAC][slaac]. This
means that stateful+stateless (on the LuCI web interface for the network
interface) must be enabled. 

Ultimately the configuration I used will result in four addresses being
assigned to each device (3 in the case of Android). These are the global address
assigned by DHCPv6, the global SLAAC address generated from the MAC address, the global [IPv6
privacy extensions][privacy] for SLAAC, and the device's automatically generated
link-local address.

[openwrt_rtn16]: http://wiki.openwrt.org/toh/asus/rt-n16
[openwrt_snapshot]: http://downloads.openwrt.org/snapshots/trunk/brcm47xx/openwrt-brcm47xx-squashfs.trx
[rtn16_unofficial]: http://openwrt.razvi.ro/
[routercfg]: www.dslreports.com/faq/16077
[moca]: https://en.wikipedia.org/wiki/Multimedia_over_Coax_Alliance
[henet]: https://www.tunnelbroker.net/
[luci]: http://luci.subsignal.org/trac
[hetest]: https://ipv6.he.net/certification/
[wiki_ula]: https://en.wikipedia.org/wiki/Unique_local_address
[slaac]: https://en.wikipedia.org/wiki/IPv6#Stateless_address_autoconfiguration_.28SLAAC.29
[privacy]: https://en.wikipedia.org/wiki/IPv6#Privacy
