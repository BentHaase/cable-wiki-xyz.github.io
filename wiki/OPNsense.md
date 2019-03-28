# OPNsense

OPNsense is a software firewall system based on FreeBSD developed by Deciso B.V. It originally started as a fork of pfsense but is developed independently based on upstream FreeBSD changes.

## Support

* IPv4 only: full support
* Dual-Stack: full support
* DS-Lite: partly supported with manual configuration for now
* PCP Support: not available

## DS-Lite Support

OPNsense can support DS-Lite tunnels with manual creation of the tunnel and route. The MTU should be set to 1460.

```
ifconfig gif0 create
ifconfig gif0 inet6 tunnel <<LOCAL IPv6 ADDRESS>> <<AFTR ADDRESS>> mtu 1460 -accept_rtadv ifdisabled
ifconfig gif0 inet 192.0.0.2 192.0.0.1 netmask 255.255.255.248
route add default -interface gif0
```

## Weblinks

* [Website](https://opnsense.org/)
