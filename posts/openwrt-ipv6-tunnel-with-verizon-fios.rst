.. title: OpenWRT IPv6 Tunnel with Verizon FIOS
.. slug: openwrt-ipv6-tunnel-with-verizon-fios
.. date: 2014/02/08 13:50:52
.. tags: ipv6 openwrt verizon slaac dhcpv6
.. link: 
.. description: 
.. type: text



So I installed OpenWRT last week on a router a friend loaned me.
Verizon's router isn't particularly bad for what it does (specifically
the Rev I Actiontec), but it really lacks customizability, and IPv6
tunneling support to correct Verizon's deficiencies.

I was able to acquire an ASUS RT-N16 last weekend and install OpenWRT on
it. The `unofficial builds`_ linked from the `wiki`_ didn't work for me,
and the wiki probably hasn't been updated for the latest available
build, so I found the `snapshot build`_ for bcrm47xx and installed it
via TFTP. The web interface (`LUCI`_) was installed and enabled.

Since I'm using Verizon, using FIOs TV, and wanted the ASUS as the
primary router in order to use it as an IPV6 tunnel endpoint , I
attempted to set up double bridging (Option 5) as described in this
`DSLReports posting`_.

Unfortunately the MI424WR Rev I is incompatible with this configuration.
I attempted to use Option 2, but the double NAT breaks Steam. Finally I
configured a static route on the Actiontec to the ASUS router's subnet
and disabled NAT and the IPv4 firewall on the ASUS router. This still
breaks UPnP and NAT is still being done on the Verizon router, but it's
something I can live with. The Rev I of the Verizon router isn't
terrible hardware-wise, it's just incompatible with custom firmware
while retaining `MoCA`_ support for the TV.

I signed up for a Hurricane Electric (HE) `tunnel`_ and configured a
tunnel. IRC does not work until one takes the `IPv6 test`_ to reach Sage
level. As a bonus, they give you a T-shirt. In order to get this to
work, IP protocol 41 had to be forwarded to the ASUS router as described
by `Bill Owens`_

As IPv6 addresses can be long and difficult to remember, I wanted to
enable DDNS for local computers in the network, which requires DHCPv6.
By default this is enabled in OpenWRT, and also sets a `Unique Local
Address`_ for the interface. Both the ULA addresses and the global
addresses from HE were assigned via DHCPv6. This would otherwise be
fine, however NetworkManager on Linux will only pick up one of the
assigned addresses, whichever gets sent last. To fix this, the ULA
address had to be cleared from the router configuration so that only the
GUA was assigned.

Android does not support DHCPv6 but will still work w/ `SLAAC`_. This
means that stateful+stateless (on the LuCI web interface for the network
interface) must be enabled.

Ultimately the configuration I used will result in four addresses being
assigned to each device (3 in the case of Android). These are the global
address assigned by DHCPv6, the global SLAAC address generated from the
MAC address, the global `IPv6 privacy extensions`_ for SLAAC, and the
device's automatically generated link-local address.

.. _unofficial builds: http://openwrt.razvi.ro/
.. _wiki: http://wiki.openwrt.org/toh/asus/rt-n16
.. _snapshot build: http://downloads.openwrt.org/snapshots/trunk/brcm47xx/openwrt-brcm47xx-squashfs.trx
.. _LUCI: http://luci.subsignal.org/trac
.. _DSLReports posting: www.dslreports.com/faq/16077
.. _MoCA: https://en.wikipedia.org/wiki/Multimedia_over_Coax_Alliance
.. _tunnel: https://www.tunnelbroker.net/
.. _IPv6 test: https://ipv6.he.net/certification/
.. _Bill Owens: http://bill-owens.blogspot.com/2011/11/hurricane-electric-ipv6-tunnel-through.html
.. _Unique Local Address: https://en.wikipedia.org/wiki/Unique_local_address
.. _SLAAC: https://en.wikipedia.org/wiki/IPv6#Stateless_address_autoconfiguration_.28SLAAC.29
.. _IPv6 privacy extensions: https://en.wikipedia.org/wiki/IPv6#Privacy
