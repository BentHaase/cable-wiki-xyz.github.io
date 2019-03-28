# EdgeOS

EdgeOS is a Network Operating System developed by Ubiquiti Networks. It is based on Vyatta and bundled with Ubiquiti EdgeRouter products.

## Support

* IPv4 only: full support
* Dual-Stack: full support
* DS-Lite: partly supported with manual configuration for now
* PCP Support: available

## Configuration examples

The following configurations were tested on an Edgerouter X with EdgeOSv1.10.1.

* Provider: Unitymedia Business
* Gateway: Provider Fritzbox 6490 in Bridge Mode
* IPv6 prefix: /56

### IPv6 Support

EdgeOS can supports IPv6 trough manual configuration via CLI. This examples uses eth0 as WAN, eth1 and eth2 as individual LAN interfaces. ([source](https://gist.github.com/mskutta/b203b73134364a78d2e3))

```
# Configure IPv6 Firewall

configure

set firewall ipv6-name IPV6WAN_IN description 'IPV6WAN to internal'
set firewall ipv6-name IPV6WAN_IN default-action drop

set firewall ipv6-name IPV6WAN_IN rule 10 action accept
set firewall ipv6-name IPV6WAN_IN rule 10 state established enable
set firewall ipv6-name IPV6WAN_IN rule 10 state related enable
set firewall ipv6-name IPV6WAN_IN rule 10 log disable
set firewall ipv6-name IPV6WAN_IN rule 10 description 'Allow established/related'

set firewall ipv6-name IPV6WAN_IN rule 20 action drop
set firewall ipv6-name IPV6WAN_IN rule 20 state invalid enable
set firewall ipv6-name IPV6WAN_IN rule 20 description 'Drop invalid state'

set firewall ipv6-name IPV6WAN_IN rule 30 action accept
set firewall ipv6-name IPV6WAN_IN rule 30 description 'Allow ICMPv6'
set firewall ipv6-name IPV6WAN_IN rule 30 log disable
set firewall ipv6-name IPV6WAN_IN rule 30 protocol icmpv6

set interfaces ethernet eth0 firewall in ipv6-name IPV6WAN_IN

commit
save
```

```
# Configure dhcpv6-pd 

configure

set interfaces ethernet eth0 dhcpv6-pd pd 0
set interfaces ethernet eth0 dhcpv6-pd pd 0 prefix-length 56
set interfaces ethernet eth0 dhcpv6-pd rapid-commit enable

set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth1 host-address ::1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth1 prefix-id :1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth1 service slaac
set interfaces ethernet eth1 ipv6 router-advert prefix ::/64

set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth2
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth2 host-address ::1
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth2 prefix-id :2
set interfaces ethernet eth0 dhcpv6-pd pd 0 interface eth2 service slaac
set interfaces ethernet eth2 ipv6 router-advert prefix ::/64

commit
save
```

### DS-Lite Support

EdgeOS can support DS-Lite tunnels with manual creation of the tunnel and route. The MTU should be set to 1460. ([source](https://blog.riywo.com/2017/04/edgerouter-x-and-ds-lite-with-ntt/))

```
configure

set interfaces ipv6-tunnel v6tun0 encapsulation ipip6
set interfaces ipv6-tunnel v6tun0 local-ip <<LOCAL IPv6 ADDRESS>>
set interfaces ipv6-tunnel v6tun0 remote-ip <<AFTR ADDRESS>>
set interfaces ipv6-tunnel v6tun0 mtu 1460

set protocols static interface-route 0.0.0.0/0 next-hop-interface v6tun0

commit
save
```

### PCP Support

EdgeOS supports PCP and NAT-PMP through a [MiniUPnP](http://miniupnp.free.fr/) deamon. This examples uses eth0 as WAN, eth1 as LAN interface. ([source](https://community.ubnt.com/t5/EdgeRouter/Enable-upnp2-on-Edgemax-lite/td-p/1439995))

```
configure

set service upnp2 listen-on eth1
set service upnp2 wan eth0

set service upnp2 nat-pmp enable
set service upnp2 secure-mode enable

commit
save
```

## Weblinks

* [EdgeRouter Product Page](https://www.ubnt.com/edgemax/edgerouter/)
