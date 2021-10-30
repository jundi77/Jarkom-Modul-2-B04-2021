# Jarkom-Modul-2-B04-2021

Konfigurasi network Foosha
```
# DHCP config for eth0
auto eth0
iface eth0 inet dhcp

# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.9.1.1
	netmask 255.255.255.0

# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.9.2.1
	netmask 255.255.255.0

```

Setting NAT di Foosha, melalui `.bashrc`
```
# NAT settings
echo Applying NAT settings
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.9.0.0/16
```


Konfigurasi network Loguetown
```
auto eth0
iface eth0 inet static
	address 10.9.1.2
	netmask 255.255.255.0
	gateway 10.9.1.1
```

Konfigurasi network Alabasta
```
auto eth0
iface eth0 inet static
	address 10.9.1.3
	netmask 255.255.255.0
	gateway 10.9.1.1
```

Menambahkan DNS master dan slave di Loguetown dan Alabasta
```
# Nameserver Config
echo nameserver 10.9.2.2 > /etc/resolv.conf # EniesLobby
echo nameserver 10.9.2.3 >> /etc/resolv.conf # Water7
echo nameserver 192.168.122.1 >> /etc/resolv.conf
```

Konfigurasi network EniesLobby
```
auto eth0
iface eth0 inet static
	address 10.9.2.2
	netmask 255.255.255.0
	gateway 10.9.2.1
```

`/etc/bind/named.conf.local` EniesLobby
```
zone "franky.B04.com" {
        type master;
        notify yes;
        also-notify { 10.9.2.3; };
        allow-transfer { 10.9.2.3; };
        file "/etc/bind/kaizoku/franky.B04.com";
};

zone "2.9.10.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.9.10.in-addr.arpa";
};
```
`/etc/bind/kaizoku/2.9.10.in-addr.arpa` EniesLobby
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                     2021100401         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.9.10.in-addr.arpa.    IN      NS      franky.B04.com.
4       IN      PTR     franky.B04.com.
;@      IN      AAAA    ::1
```

`/etc/bind/kaizoku/franky.B04.com` EniesLobby
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.B04.com. root.franky.B04.com. (
                     2021100401         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.B04.com.
@       IN      A       10.9.2.4        ; IP Skypie
www       IN      CNAME      franky.B04.com.
super       IN      A       10.9.2.4        ; IP Skypie
www.super       IN      CNAME      super.franky.B04.com.
ns1     IN      A       10.9.2.3
mecha   IN      NS      ns1
```

Konfigurasi network Water7
```
auto eth0
iface eth0 inet static
	address 10.9.2.3
	netmask 255.255.255.0
	gateway 10.9.2.1
```

Konfigurasi network Skypie
```
auto eth0
iface eth0 inet static
	address 10.9.2.4
	netmask 255.255.255.0
	gateway 10.9.2.1
```
