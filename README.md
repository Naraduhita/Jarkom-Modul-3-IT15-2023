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
## [Configure](#21) 
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

## Membuat DHCP Relay & Server
**Aura** 
- Ketikkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.71.0.0/16`
- Maka akan didapatkan `nameserver 192.168.122.1` dalam /etc/resolv.conf
- Menjadikan Aura sebagai Router (DHCP Relay)
```bash
# Instalasi DHCP relay
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf
service isc-dhcp-relay restart
```							
**Himmel**
- Setting nameserver dengan `echo "nameserver 192.168.122.1" > /etc/resolv.conf`
- Menjadikan Himmel sebagai DHCP Server
```bash
# DHCP Server
apt-get update
apt-get install isc-dhcp-server
dhcpd --version
```

## <a name="0"></a> Soal 0
**Deskripsi:**
Melakukan register domain, `riegel.canyon.yyy.com` untuk worker Laravel dan `granz.channel.yyy.com` untuk worker PHP mengarah pada worker yang memiliki IP 10.71.x.1.

- Mengubah nilai gateway pada seluruh worker menjadi berakhiran selain 1. Contohnya seperti `gateway 10.71.4.10`
- Mengubah addres pada worker agar diurutkan dari nomor 1. Contoh seperti `address 10.71.4.1`
- Pada setiap nameserver worker tambahkan IP DNS (Heiter)
```bash
echo "nameserver 192.168.122.1
nameserver 10.71.1.3" > /etc/resolv.conf
```
- Lalu pada `Heiter` install bind9 `apt-get install bind9 -y`
- Buka file named.conf.options `nano /etc/bind/named.conf.options`
- Tambahkan `allow-query{any;};` seperti berikut:
```bash
options {
        directory "/var/cache/bind";
        forwarders {192.168.122.1;};
        allow-query{ any; };
        listen-on-v6 { any; };
};
```
- Lalu restart bind9 `service bind9 restart`
- Edit file `/etc/bind/named.conf.local` dan isikan syntax berikut:
```bash
zone "riegel.canyon.IT15.com" {
        type master;
        file "/etc/bind/jarkom/riegel.canyon.IT15.com";
};
zone "granz.channel.IT15.com" {
        type master;
        file "/etc/bind/jarkom/granz.channel.IT15.com";
}; 
```
- Buka file `/etc/bind/jarkom/riegel.canyon.IT15.com`
- Tambahkan konfigurasi seperti berikut:
```bash
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.IT15.com. root.riegel.canyon.IT15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      riegel.canyon.IT15.com.
@       IN      A       10.71.4.1 ;IP Frieren
@       IN      AAAA    ::1
```
- Buka file `/etc/bind/jarkom/granz.channel.IT15.com`
- Tambahkan konfigurasi seperti berikut:
```bash
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.IT15.com. root.granz.channel.IT15.com. (
                                2       ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache $;
@       IN      NS      granz.channel..IT15.com.
@       IN      A       10.71.3.1 ;IP Lawine
@       IN      AAAA    ::1
```
**Testing**
Lakukan testing menggunakan ping pada client
- ping riegel.canyon.IT15.com
<img src="">

- ping granz.channel.IT15.com
<img src="">

## <a name="1"></a> Soal 1
**Deskripsi:** Melakukan konfigurasi sesuai dengan peta yang sudah diberikan. (telah dijelaskan di <a name="21"></a> Configure)

## <a name="2"></a> Soal 2
**Deskripsi:** Client yang melalui Switch3 mendapatkan range IP dari 10.71.3.16 - 10.71.3.32 dan 10.71.3.64 - 10.71.3.80 

- Melakukan setup untuk DHCP Server pada `/etc/dhcp/dhcpd.conf`
```bash
subnet 10.71.1.0 netmask 255.255.255.0 {
}
subnet 10.71.3.0 netmask 255.255.255.0 {
  range 10.71.3.16 10.71.3.32;
  range 10.71.3.64 10.71.3.80;
  option routers 10.71.3.10;
  option broadcast-address 10.71.3.255;
  option domain-name-servers 10.71.1.3;
  default-lease-time 180;
  max-lease-time 5760;
}
```
- Melakukan restart server DHCP `service isc-dhcp-server restart`

**Testing**
Lakukan testing pada client dengan command `ip a`
<img src="">

### <a name="3"></a> Soal 3
**Deskripsi:** 
Client yang melalui Switch4 mendapatkan range IP dari 10.71.4.12 - 10.71.4.20 dan 10.71.4.160 - 10.71.4.168
- Melakukan setup untuk DHCP Server pada `/etc/dhcp/dhcpd.conf`
```bash
subnet 10.71.1.0 netmask 255.255.255.0 {
}
subnet 10.71.4.0 netmask 255.255.255.0 {
  range 10.71.4.12 10.71.4.20;
  range 10.71.4.160 10.71.4.168;
  option routers 10.71.4.10;
  option broadcast-address 10.71.4.255;
  option domain-name-servers 10.71.1.3;
  default-lease-time 720;
  max-lease-time 5760;
}
```
- Melakukan restart server DHCP `service isc-dhcp-server restart`

### Testing
Lakukan testing pada client dengan command `ip a`
**Clinet pada Switch3**
<img src="">

**Clinet pada Switch4**
<img src="">

### <a name="4"></a> Soal 4
**Deskripsi:**  Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut.

- Mencoba `ping google.com` pada client
**Clinet pada Switch3**
<img src="">

**Clinet pada Switch4**
<img src="">

### <a name="5"></a> Soal 5
**Deskripsi:** reverse domain untuk domain utama (abimanyu)
