# Jarkom Modul 3

|    Anggota Kelompok     |     NRP     |
| ---                     | ---         |
| Oktavia Anggraeni P.    | 5027211001  |
| Brigita Naraduhita P.P. | 5027211055  |

## Daftar Isi
- [Soal 0](#0)
- [Soal 1](#1)
- [Soal 2](#2)
- [Soal 3](#3)
- [Soal 4](#4)
- [Soal 5](#5)
- [Soal 6](#6)
- [Soal 7](#7)
- [Soal 8](#8)
- [Soal 9](#9)
- [Soal 10](#10)
- [Soal 11](#11)
- [Soal 12](#12)
- [Soal 13](#13)
- [Soal 14](#14)
- [Soal 15](#15)

## Topologi
<img src="">

## Configure
- Aura (DHCP Relay): 

- Himmel (DHPCP Server):
```bash
# Static config for eth0
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.71.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.71.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.71.3.10
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.71.4.10
	netmask 255.255.255.0
```
- Heiter (DNS Server):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.1.3
	netmask 255.255.255.0
	gateway 10.71.1.1
```
- Denken (Database Server):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.2.2
	netmask 255.255.255.0
	gateway 10.71.2.1
```
- Eisen (Load Balancer):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.2.3
	netmask 255.255.255.0
	gateway 10.71.2.1
```
- Frieren (Laravel Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.4.1
	netmask 255.255.255.0
	gateway 10.71.4.10
```
- Flamme (Laravel Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.4.2
	netmask 255.255.255.0
	gateway 10.71.4.10
```
- Fern (Laravel Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.4.3
	netmask 255.255.255.0
	gateway 10.71.4.10
```
- Lawine (PHP Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.3.1
	netmask 255.255.255.0
	gateway 10.71.3.10
```
- Linie (PHP Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.3.2
	netmask 255.255.255.0
	gateway 10.71.3.10
```
- Lugner (PHP Worker):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.3.3
	netmask 255.255.255.0
	gateway 10.71.3.10
```
- Revolte, Richter, Sein, dan Stark (Client):
```bash
auto eth0
iface eth0 inet dhcp
```

## <a name="0"></a> Soal 0

- Aura 
```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.71.0.0/16

# Instalasi DHCP relay
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start

echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf

service isc-dhcp-relay restart
```
