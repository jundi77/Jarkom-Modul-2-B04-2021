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
