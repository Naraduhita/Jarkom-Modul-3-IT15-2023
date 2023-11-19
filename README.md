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
- [Soal 16](#16)
- [Soal 17](#17)
- [Soal 18](#18)
- [Soal 19](#19)
- [Soal 20](#20)


## Topologi

<p align="center">
  <img width="800" alt="Topologi" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/0acb8895-f2b7-4862-b7c9-6af87b04db1d">
</p>

[Configure](#Configure) 
- Aura (DHCP Relay): 
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
- Himmel (DHPCP Server):
```bash
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.71.1.2
	netmask 255.255.255.0
	gateway 10.71.1.1
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
- Ketika menjalankan aura maka masukkan IP himmel `10.71.1.2` --> `eth1 eth2 eth3 eth4`

<img width="500" alt="Aura_1" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/7913f6d1-337d-4a7f-9b47-e02fc0bf72ba">

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

**Heiter**
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

<img width="500" alt="No_1B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/ceaa84d7-eb94-4cc3-b7be-0e45367d0991">

## <a name="1"></a> Soal 1
**Deskripsi:** Melakukan konfigurasi sesuai dengan peta yang sudah diberikan. (telah dijelaskan di <a name="Configure"></a> Configure)
<br>
## <a name="2"></a> Soal 2
**Deskripsi:** Client yang melalui Switch3 mendapatkan range IP dari 10.71.3.16 - 10.71.3.32 dan 10.71.3.64 - 10.71.3.80 

**Himmel**
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

**Clinet pada Switch3**

<img width="400" alt="No_2A" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/15d310c8-d7ec-4f47-99ec-dc1de87abf3e">

**Clinet pada Switch4**

<img width="400" alt="No_2B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/5ed3c837-8db2-4364-98a5-dca954d179a9">

## <a name="3"></a> Soal 3
**Deskripsi:** 
Client yang melalui Switch4 mendapatkan range IP dari 10.71.4.12 - 10.71.4.20 dan 10.71.4.160 - 10.71.4.168

**Himmel**
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
<br>

## <a name="4"></a> Soal 4
**Deskripsi:**  Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut.

- Mencoba `ping google.com` pada client
  
**Clinet pada Switch3**
  
<img width="400" alt="No_4" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/8d4c6014-a26d-43c7-b016-fd3c89fb3a7e">

**Clinet pada Switch4**

<img width="400" alt="No_4B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/a9fd44d3-b575-4e70-a280-098710c15a03">

## <a name="5"></a> Soal 5
**Deskripsi:** reverse domain untuk domain utama (abimanyu)

**Himmel**
- Diperlukan penggunaan fungsi default-lease-time dan max-lease-time dengan satuan detik.
- Switch3 dapat mengalokasikan IP selama 3 menit, sedangkan Switch4 dapat memberikan IP selama 12 menit. Oleh karena itu, Switch3 memerlukan waktu 180 detik dan Switch4 memerlukan waktu 720 detik. Untuk max-lease-time, diatur pada nilai 96 menit, yang setara dengan 5760 detik.
```bash
echo  "subnet 10.71.1.0 netmask 255.255.255.0 {
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
subnet 10.71.4.0 netmask 255.255.255.0 {
  range 10.71.4.12 10.71.4.20;
  range 10.71.4.160 10.71.4.168;
  option routers 10.71.4.10;
  option broadcast-address 10.71.4.255;
  option domain-name-servers 10.71.1.3;
  default-lease-time 720;
  max-lease-time 5760;
}" > /etc/dhcp/dhcpd.conf
```
- Melakukan restart server DHCP `service isc-dhcp-server restart`

### Testing
Melakukan ping google pada client

**Clinet pada Switch3**

<img width="400" alt="No_5-Client1" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/d926fe08-eba9-4893-b80f-cf54f2923984">

**Clinet pada Switch4**

<img width="400" alt="No_5-Client2" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/5556fcf5-235a-450c-a70a-7670eca119ec">

## <a name="6"></a> Soal 6
**Deskripsi:** <br> 
Konfigurasi virtual host untuk website dari link zip dengan menggunakan php 7.3

**Worker PHP (Lawine, Linie, Lugner)**
- Pertama perlu dilakukan setup dasar IP pada `/etc/resolv.conf` agar terhubung dengan DNS Server
```
echo 'nameserver 192.168.122.1
nameserver 10.71.1.3' > /etc/resolv.conf
```
- Lakukan instalasi nginx, php 7.3, lynx, dan unzip yang dibutuhkan dalam pengerjaannya
```
apt-get update && apt install nginx php php-fpm -y
php -v

apt-get install lynx -y

apt-get install unzip -y
```

- Download dan setup resource yang diberikan
```
cd /var/www/

curl -L --insecure "https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1" -o riegel

unzip riegel -d riegel.canyon.IT15
mv riegel.canyon.IT15/modul-3/* riegel.canyon.IT15
rm -r riegel 
```

- Melakukan konfigurasi pada `/etc/nginx/sites-available/riegel.canyon.IT15` dan membuat symlink sebagai berikut
```
echo '
server {
    listen 80;

    root /var/www/riegel.canyon.IT15;
    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

 }' > /etc/nginx/sites-available/riegel.canyon.IT15

ln -s /etc/nginx/sites-available/riegel.canyon.IT15 /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default
```

- Restart nginx dan php 7.3 
```
service nginx restart
nginx -t
service php7.3-fpm start
service php7.3-fpm status
```

**Masuk ke `client`, misalnya pada `Richter`**
- Setup dasar IP pada client agar terhubung dengan DNS Server
```
echo 'nameserver 192.168.122.1
nameserver 10.71.1.3' > /etc/resolv.conf
```
- Instalasi lynx
```
apt-get update
apt-get install lynx -y
```
### Testing
- Jalankan command pada `client` menggunakan IP masing-masing worker
```
lynx 10.71.3.1 #lawine
lynx 10.71.3.2 #linie
lynx 10.71.3.3 #lugner
```

#### Screenshot:
- Lawine:
<img src="https://i.ibb.co/MkMrQZY/6-riegel-lawine.png">

- Linie:
<img src="https://i.ibb.co/NLwwq36/6-riegel-linie.png">

- Lugner:
<img src="https://i.ibb.co/NsHpgP0/6-riegel-lugner.png">

## <a name="7"></a> Soal 7
**Deskripsi:**: <br>
Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
a.	Lawine, 4GB, 2vCPU, dan 80 GB SSD.
b.	Linie, 2GB, 2vCPU, dan 50 GB SSD.
c.	Lugner 1GB, 1vCPU, dan 25 GB SSD.
aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

- Buka kembali setting DNS-server dan arahkan ke IP Load Balancer `Eisen`
```
echo '
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
@       IN      A       10.71.2.3 ;IP Eisen
@       IN      AAAA    ::1
' > /etc/bind/jarkom/riegel.canyon.IT15.com

echo '
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.IT15.com. root.granz.channel.IT15.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache $;
@       IN      NS      granz.channel.IT15.com.
@       IN      A       10.71.2.3 ;IP Eisen
@       IN      AAAA    ::1
' > /etc/bind/jarkom/granz.channel.IT15.com
```
**Eisen**
- Instalasi dan setup 
```
apt-get update && apt install nginx php php-fpm -y

apt-get install lynx -y
```

- Melakukan konfigurasi nginx pada `/etc/nginx/sites-available/lb-jarkom` 
```
echo 'upstream backend  {  
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```

**Masuk ke client misalnya `Richter`**
```
apt-get install apache2-utils -y
ab -n 1000 -c 100 http://riegel.canyon.IT15.com/
```
atau 
```
ab -n 1000 -c 100 http://10.71.2.3/
```
### Screenshot
Brenchmarking hingga 1000 request:
<img src="https://i.ibb.co/hdLxwdN/7-riegel-1000-100.png"> <br>
Persentase request dan waktu:
<img src="https://i.ibb.co/r4MkDNQ/7-riegel-request-precentage.png">

## <a name="8"></a> Soal 8
**Deskripsi:**:<br>
Karena diminta untuk menuliskan grimoire, buat analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
- Nama Algoritma Load Balancer
- Report hasil testing pada Apache Benchmark
- Grafik request per second untuk masing masing algoritma. 
- Analisis

- Algoritma yang akan diterapkan
`Round Robin`, `Weighted Round Robin` `Least Connection` `IP Hash` dan `Generic Hash`

- Instalasi pada worker dan client
```
apt-get install apache2-utils -y
apt-get install htop -y
```
1. Round Robin
```
echo 'upstream backend  {  
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```

#### Jalankan command berikut pada `client`, contohnya Richter
```
cat /var/log/nginx/access.log| grep "GET" | wc -l
cat /var/log/nginx/riegel.canyon.IT15_access.log| grep "GET" | wc -l
ab -n 200 -c 10 http://riegel.canyon.IT15.com/
```
### Screenshot
Tampilan jumlah GET yang diperoleh dalam proses algoritma:
<img src="https://i.ibb.co/r23gCQt/round-robbin-get.png"> <br>
Hasil HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/kqYJPPk/round-robbin-htop.png">

2. Weighted Round-Robin
```
echo 'upstream backend  {  
    server 10.71.3.1 weight=4; # lawine
    server 10.71.3.2 weight=2; # linie
    server 10.71.3.3 weight=1; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom
ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```

#### Jalankan command berikut pada `client`, contohnya Richter
```
cat /var/log/nginx/access.log| grep "GET" | wc -l
cat /var/log/nginx/riegel.canyon.IT15_access.log| grep "GET" | wc -l
ab -n 200 -c 10 http://riegel.canyon.IT15.com/
```
### Screenshot:
Tampilan jumlah GET yang diperoleh dalam proses algoritma:
<img src="https://i.ibb.co/kKDmxT1/weighted-roundrobin-get.png"> <br>
Hasil HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/DGKZBL2/weighted-roundrobin-htop.png">

3. Least Connection
```
echo 'upstream backend  {  
    least_conn;
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```
#### Jalankan command berikut pada `client`, contohnya Richter
```
cat /var/log/nginx/access.log| grep "GET" | wc -l
cat /var/log/nginx/riegel.canyon.IT15_access.log| grep "GET" | wc -l
ab -n 200 -c 10 http://riegel.canyon.IT15.com/
```
### Screenshot:
Tampilan jumlah GET yang diperoleh dalam proses algoritma:
<img src="https://i.ibb.co/JkWPKym/least-conn-get.png"> <br>
Hasil HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/QQDwJyZ/least-conn-htop.png">

4. IP Hash
```
echo 'upstream backend  {  
    ip_hash;
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```
#### Jalankan command berikut pada `client`, contohnya Richter
```
cat /var/log/nginx/access.log| grep "GET" | wc -l
cat /var/log/nginx/riegel.canyon.IT15_access.log| grep "GET" | wc -l
ab -n 200 -c 10 http://riegel.canyon.IT15.com/
```
### Screenshot:
Tampilan jumlah GET yang diperoleh dalam proses algoritma:
<img src="https://i.ibb.co/PMX63T9/ip-hash-get.png"> <br>
Hasil HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/YcBPCyq/ip-hash-htop.png">

5. Generic Hash
```
echo 'upstream backend  {  
    hash $request_uri consistent;
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    Host $http_host;
        }
}' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```
#### Jalankan command berikut pada `client`, contohnya Richter
```
cat /var/log/nginx/access.log| grep "GET" | wc -l
cat /var/log/nginx/riegel.canyon.IT15_access.log| grep "GET" | wc -l
ab -n 200 -c 10 http://riegel.canyon.IT15.com/
```
### Screenshot:
Tampilan jumlah GET yang diperoleh dalam proses algoritma:
<img src="https://i.ibb.co/9Yh3Zsp/generichash-get.png"> <br>
Hasil HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/P98mHng/generichash-htop.png">

## <a name="9"></a> Soal 9
**Deskripsi:**: <br>
Dengan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.

- Ubah kembali isi file `/etc/nginx/sites-available/lb-jarkom` pada `Eisen` untuk Round Robin seperti pada soal no. 8 sebelumnya

- Jalankan command berikut pada `client`
```
ab -n 100 -c 10 http://riegel.canyon.IT15.com/
```
- Jalankan htop pada 3 worker
Screenshot HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/Lr7Yf0D/roundrobin-3worker.png">

- Jalankan htop pada 2 worker
Stop salah satu worker, misalnya Lugner `service nginx stop`
Screenshot HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/jrfCMJg/roundrobin-2worker.png">

- Jalankan htop pada 1 worker
Stop 1 worker lagi, misalnya Linie `service nginx stop`. Jadi hanya 1 worker yang berjalan, yaitu Lawine
Screenshot HTOP dengan node dari atas kiri ke kanan dan lanjut ke kiri bawah adalah Richter, Lawine, Linie, dan Lugner:
<img src="https://i.ibb.co/Np2bMnm/roundrobin-1worker.png">

## <a name="10"></a> Soal 10
**Deskripsi:** <br>
Tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: `netics` dan password: `ajkIT15`. Simpan file “htpasswd” nya di /etc/nginx/rahasisakita/ 

- Lakukan instalasi apache2 pada `Eisen `
```
apt-get install apache2-utils -y
```
- Buat direktori lalu atur username dan password
```
mkdir /etc/nginx/rahasisakita/
htpasswd -c -b /etc/nginx/rahasisakita/.htpasswd netics ajkIT15
```
- Tambahkan konfigurasi `auth_basic` pada `/etc/nginx/sites-available/lb-jarkom`
```
echo '
upstream backend  {
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_fo$
        proxy_set_header    Host $http_host;
        
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd       
        }
}' > /etc/nginx/sites-available/lb-jarkom

unlink /etc/nginx/sites-enabled/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```
- Jalankan command berikut pada `client`
```
lynx riegel.canyon.IT15.com
```
### Screenshot
Tampilan input username:
<img src="https://i.ibb.co/QPvzZ1D/10-tampilan-input-username.png"> <br>
Tampilan input password:
<img src="https://i.ibb.co/jgYvDrF/10-tampilan-input-password.png"> <br>
Tampilan saat berhasil masuk:
<img src="https://i.ibb.co/VY3N5jp/10-tampilan-saat-berhasil-masuk.png"> <br>
Tampilan saat gagal masuk:
<img src="https://i.ibb.co/NjfvWgh/10-tampilan-saat-gagal-masuk.png"> <br>

## <a name="11"></a> Soal 11
**Deskripsi:** <br>
Buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id `hint: (proxy_pass)`

- Lanjutkan no. 10 sebelumnya dengan menambahkan `proxy_pass` pada `/etc/nginx/sites-available/lb-jarkom`
```
echo '
upstream backend  {
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;       
    }
    
    location ~ /its {
        proxy_pass https://www.its.ac.id;
        proxy_set_header Host www.its.ac.id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}' > /etc/nginx/sites-available/lb-jarkom

unlink /etc/nginx/sites-enabled/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```

- Jalankan command berikut pada `client`
```
lynx riegel.canyon.IT15.com/its
```
Tampak bahwa cara menjalankannya agak berbeda karena dengan penambahan `/its` maka akan mengarah ke halaman `https://www.its.ac.id` <br>
### Screenshot
Proses masuk ke halaman its.ac.id:
<img src="https://i.ibb.co/HVhn7kZ/11-proses-masuk-ke-halaman-its-ac-id.png"> <br>
Tampilan halaman home its.ac.id:
<img src="https://i.ibb.co/QQCHRCg/11-tampilan-halaman-its-ac-id.png"> <br>
Teks berhighlight pada halaman juga dapat di klik:
<img src="https://i.ibb.co/KbV2G1s/11-dapat-juga-mengklik-text-yang-diinginkan-seperti-pada-website.png">

## <a name="12"></a> Soal 12
**Deskripsi:**
Selanjutnya LB hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168

- Untuk membatasi akses, tambahkan code `allow IP` pada LB `Eisen` bagian `location /`
```
echo '
upstream backend  {
    server 10.71.3.1; # lawine
    server 10.71.3.2; # linie
    server 10.71.3.3; # lugner
}

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;  

#tambahkan kode berikut
        allow 10.71.3.69;
        allow 10.71.3.70;
        allow 10.71.4.167;
        allow 10.71.4.168;
        deny all;     
    }
    
    location ~ /its {
        proxy_pass https://www.its.ac.id;
        proxy_set_header Host www.its.ac.id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}' > /etc/nginx/sites-available/lb-jarkom

unlink /etc/nginx/sites-enabled/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

rm -r /etc/nginx/sites-enabled/default

service nginx restart
```

- Jalankan command `ip a` pada client dan lihat pada hardware ethernet eth0.

- Masuk ke `Himmel` DHCP Sever dan tambahkan script berikut pada `/etc/dhcp/dhcpd.conf`
```
host Richter {
    hardware ethernet da:80:6f:3f:4b:9c;
    fixed-address 10.71.3.69;
    option host-name \"Richter"\;
}
```
- Kemudian restart dhcp sebagai berikut
```
service isc-dhcp-server restart
```

- Masuk kembali ke `client` kemudian tambahkan konfigurasi pada `/etc/network/interfaces`
```
echo '
auto eth0
iface eth0 inet dhcp
hwaddress ether da:80:6f:3f:4b:9c
' > /etc/network/interfaces
```

## <a name="13"></a> Soal 13
**Deskripsi:***
Semua data yang diperlukan, diatur pada Denken sebagai database server dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

**Denken**
- Melakukan instalasi mariaDB `apt-get install mariadb-server -y`
- Menyalakan mysql server `service mysql start`
- Menambahkan konfigurasi pada `/etc/mysql/my.cnf`
```bash
echo "[mysqld]
skip-networking=0
skip-bind-address" > /etc/mysql/my.cnf
```
- Selanjutnya buka `mysql` dan jalankan command berikut:
```bash
CREATE USER 'kelompokIT15'@'%' IDENTIFIED BY 'passwordIT15';
CREATE USER 'kelompokIT15'@'localhost' IDENTIFIED BY 'passwordIT15';
CREATE DATABASE dbkelompokIT15;
GRANT ALL PRIVILEGES ON *.* TO 'kelompokIT15'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompokIT15'@'localhost';
FLUSH PRIVILEGES;
SHOW DATABASES;
```

<img width="400" alt="No_13A" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/f89cf471-6842-49c0-9b34-2b6e4912c1dd">

- Lalu restart mysql `service mysql restart`

**Worker Laravel**
- Lakukan instalasi `apt-get install mariadb-client -y`
- Jalankan command berikut:
```bash
mariadb --host=10.71.2.2 --port=3306 --user=kelompokIT15 --password
```
- Masukkan password, maka setelah itu akan masuk ke mysql.
- Jalankan perintah `SHOW DATABASES` untuk menampilkan database yang sudah diinputkan di denken.

### Testing

<img width="400" alt="No_13C" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/47806a18-9a46-47d5-b613-7c9849fa60f1">

## <a name="14"></a> Soal 14
**Deskripsi:**
Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer.

**Worker Laravel**
- Lakukan instalasi dan jalankan command berikut:
```bash
apt-get update
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2
apt-get install nginx -y
apt-get install git -y
apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
```
- Jalankan command untuk mengunduh composer
```bash
wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer
```
- Jalankan `composer -V` untuk mengetahui versi composer 
- Masuk direktori `/var/www`
- Lakukan cloning dari [link Github](https://github.com/martuafernando/laravel-praktikum-jarkom) yang telah diberikan
```bash
git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git
cd laravel-praktikum-jarkom
composer update
```
- Ubah file `.env.example` menjadi `.env`
```bash
cp /var/www/laravel-praktikum-jarkom/.env.example /var/www/laravel-praktikum-jarkom/.env
```
- Lalu ubah konfigurasi menjadi sebagai berikut:
```bash
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.71.2.2 
DB_PORT=3306
DB_DATABASE=dbkelompokIT15
DB_USERNAME=kelompokIT15
DB_PASSWORD=passwordIT15
```
- Setelah selesai jalankan command berikut:
```bash
php artisan migrate:fresh
php artisan db:seed --class=AiringsTableSeeder
php artisan key:generate
php artisan config:cache
php artisan migrate
php artisan db:seed
php artisan storage:link
php artisan jwt:secret
php artisan config:clear
```

**Denken**
- Lakukan pengecekan pada database di Denken sebagai berikut:
```bash
cd /etc/mysql
mysql -u kelompokIT15 -p
SHOW DATABASES;
USE dbkelompokIT15;
SHOW DATABASES;
```

<img width="400" alt="No_14C" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/fe1f965f-31ac-4bd7-a4ac-b7d79b34c8f9">

<img width="300" alt="No_14D" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/8f88b4d1-2bbc-4635-b1a9-ab964fdd2a72">

**Worker Laravel**
- Untuk melakukan deployment pada masing-masing worker, tambahkan virtual host pada file `/etc/nginx/sites-available/jarkom-deploy`
```bash
server {

    listen [PORT];

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files \$uri \$uri/ /index.php?\$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/jarkom-deploy_error.log;
    access_log /var/log/nginx/jarkom-deploy_access.log;
}
```
- Jalankan command berikut:
```bash
ln -s /etc/nginx/sites-available/jarkom-deploy /etc/nginx/sites-enabled/
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service php8.0-fpm start
```

### Testing
- Setelah melakukan konfigurasi pada setiap worker, maka jalankan command berikut:
```bash
service nginx start
lynx localhost:[PORT]
```
<img width="700" alt="No_14G_Hasil" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/220c4d14-8050-4c3d-b04d-572c8554d167">

## <a name="15"></a> Soal 15
**Deskripsi:**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Untuk POST /auth/register 

**Worker Laravel**

Menggunakan worker `Frieren` yang nantikan akan melakukan testing pada client `Revolte`.
- Lakukan instalasi 
```bash
apt-get update
apt install apache2-utils -y
```
- Menggunakan file `.json` yang akan dikirimkan ke endpoint `/api/auth/register`
```bash
echo '
{
  "username": "kelompokIT15",
  "password": "passwordIT15"
}' > register.json
```

### Testing
- Untuk melakukan testing, jalankan command berikut pada `Revolte`:
```bash
ab -n 100 -c 10 -p register.json -T application/json http://10.71.4.1:8001/api/auth/register
```
Terdapat kesalahan dalam mengirimkan 100 permintaan. Ini disebabkan oleh persyaratan keunikan pada tabel pengguna (users), di mana setiap data username yang dimasukkan harus bersifat unik. Akibatnya, hanya satu permintaan yang dapat dijalankan, sementara 99 permintaan lainnya tidak dapat diproses.

<img width="500" alt="No_15B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/b3543ec6-fae8-43a3-86de-10abafc2b4ac">

## <a name="16"></a> Soal 16
**Deskripsi:**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Untuk POST /auth/login

**Worker Laravel**

Menggunakan worker `Frieren` yang nantikan akan melakukan testing pada client `Revolte`.
- Lakukan instalasi 
```bash
apt-get update
apt install apache2-utils -y
```
- Menggunakan file `.json` yang akan dikirimkan ke endpoint `/api/auth/login`
```bash
echo '
{
  "username": "kelompokIT15",
  "password": "passwordIT15"
}' > login.json
```

### Testing
- Untuk melakukan testing, jalankan command berikut pada `Revolte`:
```bash
ab -n 100 -c 10 -p login.json -T application/json http://10.71.4.1:8001/api/auth/ogin
```

<img width="400" alt="No_16B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/2ebdc8e8-4654-43c6-be48-463b862f098d">


## <a name="17"></a> Soal 17
**Deskripsi:**
Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Untuk GET /me

**Worker Laravel**

Lakukan instalasi 
```bash
apt-get update
apt-get install jq
```
- Mengirim permintaan HTTP POST ke URL dengan menggunakan data JSON dari file login.json `curl -X POST -H "Content-Type: application/json" -d @login.json http://10.71.4.1:8001/api/auth/login > login_output.txt`
- Menggunakan jq untuk mengekstrak nilai token dari file login_output.txt `token=$(cat login_output.txt | jq -r '.token')/me`

### Testing
- Untuk melakukan testing, jalankan command berikut pada `Revolte`:
```bash
ab -n 100 -c 10 -H "Authorization: Bearer $token" http://10.71.4.1:8001/api/me
```

<img width="500" alt="No_17" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/62bbb7f6-21e2-4cc5-955f-235037e0e28b">


<img width="400" alt="No_17B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/0df8e6ae-6e57-47b0-b594-ca578363e796">

## <a name="18"></a> Soal 18
**Deskripsi:**
Mengimplementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern. Dilakukan konfigurasi pada Load Balancer (Eisen).

**Eisen**

- Melakukan instalasi
```bash
apt-get update
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y
```
- Melakukan konfigurasi sebagai berikut
```bash
service nginx start
echo 'upstream worker { #(round-robin(default), ip_hash, least_conn, hash $request_uri consistent)
    server 10.71.4.1:8001;
    server 10.71.4.2:8002;
    server 10.71.4.3:8003;
    }

server {
    listen 80;
    server_name riegel.canyon.IT15.com;

    location / {
        proxy_pass http://worker;
    }
}
' > /etc/nginx/sites-available/laravel_worker

ln -s /etc/nginx/sites-available/laravel_worker /etc/nginx/sites-enabled/

service nginx restart
```
**Heiter**
- Mengubah IP pada riegel.canyon.IT15.com agar mengarah ke `Eisen`

### Testing
- Untuk melakukan testing, jalankan command berikut pada `Revolte`:
```bash
ab -n 100 -c 10 -H "Authorization: Bearer $token" http://10.71.4.1:8001/api/me
```

<img width="431" alt="No_18" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/23ffa98e-f65e-42fb-bb2d-f08c1d0711a8">

- Menampilkan log pada worker laravel `cat /var/log/nginx/jarkom-deploy_access.log`

<img width="400" alt="No_18B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/7abd9c97-bd93-444d-a3bc-4d6b93c2f1b4">

## <a name="19"></a> Soal 19
**Deskripsi:**
Melakukan tersting pada:
- pm.max_children: Menentukan jumlah maksimal proses anak (child processes) yang dapat dihasilkan oleh PHP-FPM. 
- pm.start_servers: Menentukan jumlah proses anak yang akan dihasilkan saat PHP-FPM pertama kali dijalankan atau setelah di-restart.
- pm.min_spare_servers: Menentukan jumlah minimum proses anak yang akan tetap aktif dan siap menangani permintaan meskipun jumlah permintaan menurun.
- pm.max_spare_servers: Menentukan jumlah maksimal proses anak yang akan tetap aktif saat permintaan rendah atau tidak ada permintaan.

**Worker Laravel**

Melakukan 3 kali testing:

- Testing 1
```bash
#!/bin/bash
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

pm = dynamic
pm.max_children = 9
pm.start_servers = 4
pm.min_spare_servers = 2
pm.max_spare_servers = 11' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```
- Testing 2
```bash
#!/bin/bash
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

pm = dynamic
pm.max_children = 15
pm.start_servers = 10
pm.min_spare_servers = 12
pm.max_spare_servers = 20' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

- Testing 3
```bash
#!/bin/bash
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 12
pm.start_servers = 16
pm.min_spare_servers = 7
pm.max_spare_servers = 14' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

### Testing

Pada setiap testing lakukan percobaan pada client

<img width="600" alt="No_19_test1" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/fe41ba67-5c8b-4276-9030-449326ef82d4">


## <a name="20"></a> Soal 20
**Deskripsi:**

**Eisen**

- Tambahkan konfigurasi `least_conn`
- Lalu restart nginx `service nginx restart`

### Testing

<img width="400" alt="No_20B" src="https://github.com/Naraduhita/Jarkom-Modul-3-IT15-2023/assets/102397053/235ad84f-7d34-4df7-92da-5cc2ff297dc4">

