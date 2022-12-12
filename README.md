# Jarkom-Modul-5-F04-2022

Nama Anggota:

1. Ida Bagus Kade Rainata Putra Wibawa 5025201235 (Menghitung CIDR mengerjakan soal 2,3,4,5)
2. Hilmi (Mengerjakan Soal ABCD Konfigurasi)


## A

Berikut adalah topologi dari jaringan pada soal shift:
![image](https://user-images.githubusercontent.com/86661387/206892132-18009c0a-c14f-4a67-89d3-f14ec19c01c6.png)

karena topologi diatas, kami membuat settingan GNS# sebagai berikut

```txt
# Strix

auto eth0
iface eth0 inet dhcp

# Ostania
auto eth1
iface eth1 inet static
 address 192.201.20.1
 netmask 255.255.255.252
 
# Westalis
auto eth2
iface eth2 inet static
 address 192.201.8.1
 netmask 255.255.255.252
 
##########################

# Ostania

# Strix
auto eth0
iface eth0 inet static
 address 192.201.20.2
 netmask 255.255.255.252
 
# Blackbell
auto eth1
iface eth1 inet static
 address 192.201.16.1
 netmask 255.255.255.0

# Briar
auto eth2
iface eth2 inet static
 address 192.201.18.1
 netmask 255.255.255.0

# Garden & SSS
auto eth3
iface eth3 inet static
 address 192.201.17.1
 netmask 255.255.255.248
 
##########################

# Blackbell

# Ostania
auto eth0
iface eth0 inet static
 address 192.201.16.2
 netmask 255.255.255.0
 gateway 192.201.16.1
 
##########################

# Garden

# Ostania
auto eth0
iface eth0 inet static
 address 192.201.17.3
 netmask 255.255.255.248
 gateway 192.201.17.1
 
##########################

# SSS

# Ostania
auto eth0
iface eth0 inet static
 address 192.201.17.2
 netmask 255.255.255.248
 gateway 192.201.17.1
 
##########################

# Briar

# Ostania
auto eth0
iface eth0 inet static
 address 192.201.18.2
 netmask 255.255.255.0
 gateway 192.201.18.1
 
##########################

# Desmond

# Westalis
auto eth0
iface eth0 inet static
 address 192.201.0.2
 netmask 255.255.252.0
 gateway 192.201.0.1
 
##########################

# Forger

# Westalis
auto eth0
iface eth0 inet static
 address 192.201.4.2
 netmask 255.255.255.192
 gateway 192.201.4.1
 
##########################

# WISE

# Westalis
auto eth0
iface eth0 inet static
 address 192.201.4.67
 netmask 255.255.255.248
 gateway 192.201.4.65
 
##########################

# Eden

# Westalis
auto eth0
iface eth0 inet static
 address 192.201.4.66
 netmask 255.255.255.248
 gateway 192.201.4.65
 
##########################

# Westalis

# Strix
auto eth0
iface eth0 inet static
 address 192.201.8.2
 netmask 255.255.255.252
 
# Desmond
auto eth1
iface eth1 inet static
 address 192.201.0.1
 netmask 255.255.252.0

# Forger
auto eth2
iface eth2 inet static
 address 192.201.4.1
 netmask 255.255.255.192

# WISE & Eden
auto eth3
iface eth3 inet static
 address 192.201.4.65
 netmask 255.255.255.248
 
##########################
```

keterangan:

```txt
Eden adalah DNS Server
WISE adalah DHCP Server
  Garden dan SSS adalah Web Server
  Jumlah Host pada Forger adalah 62 host
  Jumlah Host pada Desmond adalah 700 host
  Jumlah Host pada Blackbell adalah 255 host
  Jumlah Host pada Briar adalah 200 host
```

Karena Eden adalah DNS server, jalankan script berikut pada Eden:

```bash
# Setting up Eden as DNS Server
## Install Bind
apt-get update
apt-get install bind9 -y
```

![image](https://user-images.githubusercontent.com/86661387/206892150-398dea41-ce31-4a03-ac3e-5db1b6b6f824.png)

Lalu lanjutkan implementasi sesuai dengan [modul](https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/master/Modul-2/DNS) hingga eden dapat melakukan ping pada `google.com`.

Lalu berikut adalah setting pada Garden dan SSS agar menjadi web server:

```bash
# Setting up Garden and SSS as Web Server
## Lynx
apt-get update -y
apt-get install lynx -y
## Apache 
apt-get install apache2 -y
service apache2 start
## PHP
apt-get install php -y
php -v
echo "<?php
    phpinfo();
?>" >> /var/www/html/index.php 
apt-get install libapache2-mod-php7.0
service apache2 restart
cd /etc/apache2/sites-available
cp 000-default.conf default-8080.conf
```

![image](https://user-images.githubusercontent.com/86661387/206892175-9faeea72-cf78-4be4-8b12-8c8dbfd7c600.png)

![image](https://user-images.githubusercontent.com/86661387/206892181-11c1f08d-18c6-403f-9bca-1153a6b4360b.png)

## B

Perhitungan subnet yang kami gunakan adalah teknik CIDR dengan hasil sebagai berikut:
![MODUL 5 CIDR TREE](https://user-images.githubusercontent.com/86661387/206892236-5f9ec3bc-81be-47bc-ad73-e490e9166a06.png)

## C

Berikut adalah routing yang kami lakukan:

```txt
# Strix
#192.201.0.0/21 via 192.201.8.2
#192.201.16.0/22 via 192.201.20.2

route add -net 192.201.0.0 netmask 255.255.248.0 gw 192.201.8.2
route add -net 192.201.16.0 netmask 255.255.252.0 gw 192.201.20.2

##########################

# Westalis
#0.0.0.0/0 via 192.201.8.1

route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.201.8.1

##########################

# Ostania
#0.0.0.0/0 via 192.201.20.1

route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.201.20.1

##########################

nano ~/route.sh
echo "bash ~/route.sh" >> /root/.bashrc
nano ~/.bashrc
```

## D

Karena WISE adalah DHCP server, implementasikan DHCP server pada WISE dan implementasikan DHCP client pada selain WISE dengan implementasi [berikut](https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/master/Modul-3/DHCP#12-implementasi).

## NO 1

## NO 2

## NO 3

## NO 4

## NO 5

Jalankan script berikut:

```bash
apt-get install -y netcat
# Menjalankan command netcat di Garden
nc -l -p 80
 
# Menjalankan command netcat di SSS
nc -l -p 443
# Menjalankan command netcat di client
nc 192.201.19.2 80
nc 192.201.19.3 443
```
