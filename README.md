# Jarkom-Modul-3-A03-2022

- Konfigurasi Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.170.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.170.2.1
	netmask 255.255.255.0


auto eth3
iface eth3 inet static
	address 192.170.3.1
	netmask 255.255.255.0
```
- Konfigurasi SSS
```
auto eth0
iface eth0 inet static
        address 192.170.1.2
        netmask 255.255.255.0
        gateway 192.170.1.1

 auto eth0
 iface eth0 inet dhcp

```

- Konfigurasi Garden
```

auto eth0
iface eth0 inet static
        address 192.170.1.3
        netmask 255.255.255.0
        gateway 192.170.1.1

 auto eth0
 iface eth0 inet dhcp


 ```


- Konfigurasi Wise
```
auto eth0
iface eth0 inet static
	address 192.170.2.2
	netmask 255.255.255.0
	gateway 192.170.2.1
  
```

 
 - Konfigurasi Berlint
 ```
auto eth0
iface eth0 inet static
	address 192.170.2.3
	netmask 255.255.255.0
	gateway 192.170.2.1
  ```
  
 - Konfigurasi Westalis
 ```
auto eth0
iface eth0 inet static
	address 192.170.2.4
	netmask 255.255.255.0
	gateway 192.170.2.1
  ```
 - Konfigurasi Eden
```
auto eth0
iface eth0 inet static
        address 192.170.3.2
        netmask 255.255.255.0
        gateway 192.170.3.1

 auto eth0
 iface eth0 inet dhcp
 ```
- Konfigurasi NewstonCastle
```
auto eth0
iface eth0 inet static
        address 192.170.3.3
        netmask 255.255.255.0
        gateway 192.170.3.1

 auto eth0
 iface eth0 inet dhcp
 ```


- Konfigurasi KemonoPark
```
auto eth0
iface eth0 inet static
        address 192.170.3.4
        netmask 255.255.255.0
        gateway 192.170.3.1

 auto eth0
 iface eth0 inet dhcp
 ```


## No.1 
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

#### Konfigurasi .bashrc:

- Wise(DNS Server)
```
echo  'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
apt-get install nano -y
service bind9 start
```

- Westalis(DHCP Server)
```
echo  'nameserver 192.168.122.1' >> /etc/resolv.conf
apt-get update
apt-get upgrade -y
apt-get install isc-dhcp-server -y
```

- Berlint(Proxy Server)
```
echo  'nameserver 192.168.122.1' >> /etc/resolv.conf
apt-get update
apt-get upgrade -y
apt-get install nano -y
apt-get install squid -y
service squid restart
service squid status
```

## No.2 
dan Ostania sebagai DHCP Relay

- Ostania(DHCP Relay)
```
echo  'nameserver 192.168.122.1' >> /etc/resolv.conf
apt-get update
apt-get upgrade -y
apt-get install nano -y
apt-get install isc-dhcp-relay -y
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.170.0.0/16
```
Lalu, melakukan pengisian untuk dhcp server dan interfacenya

```
echo '
SERVERS="192.170.2.4"
INTERFACES="eth1 eth2 eth3"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
```
## No.3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 
```
range 192.170.1.50 192.170.1.88;
range 192.170.1.120 192.170.1.155;
```
## No.4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85 
```
range 192.170.3.10 192.170.3.30;
range 192.170.3.60 192.170.3.85;
```
## No.5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut. 
```
option domain-name-servers 192.170.2.2;
``` 
## No.6 
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit. 

```
default-lease-time 300;
  max-lease-time 6900;
  
default-lease-time 600;
  max-lease-time 6900;
```

dan diatur interfacenya dengan

```
echo '
INTERFACES="eth0"
' >  /etc/default/isc-dhcp-server
```

## No. 7
Untuk memberikan fixed-address kepada client, maka kita akan menambah konfigurasi dari file /etc/dhcp/dhcpd.conf dengan konfigurasi berikut

```
host Eden {
        hardware ethernet 3a:56:6c:1a:3a:1e;
        fixed-address 192.170.3.13;
}
```

dan untuk konfigurasi interfacenya sendiri diubah dengan dibawahnya ditambahkan hardware ethernet yang sama dengan konfigurasi /etc/dhcp/dhcpd.conf sebagai berikut

```
hwaddress ether 3a:56:6c:1a:3a:1e;
```

Restart DHCP Server dengan command ``` service isc-dhcp-server restart ``` 

# Proxy
Install terlebih dahulu ``` lynx ``` pada salah satu client yang akan digunakan
## No. 1
Back-up terlebih dahulu file /etc/squid/squid.conf dengan menggunakan menggunakan command ``` mv /etc/squid/squid.conf /etc/squid/squid.conf.bak ```
Setelah itu, buatlah file /etc/squid/squid.conf dan isilah dengan konfigurasi berikut

```
include /etc/squid/acl.conf

http_port 8080
http_access deny working_1
http_access allow all
visible_hostname Berlint
```

Lalu, buatlah file acl.conf dan isilah dengan konfigurasi berikut

```
acl working_1 time MTWHF 08:00-17:00
```

Restart Squid dengan command ``` service squid restart ```
Ketika kita mengakses sebuah website didalam jam & hari kerja, maka akan muncul tampilan seperti ini

![image](https://user-images.githubusercontent.com/72655301/201680363-b8c03932-7416-4e76-b5ca-659548dd3ec6.png)


## No. 2

Buatlah file allowed-sites.acl dan isilah dengan konfigurasi berikut

```
loid-work.com
franky-work.com
```

Setelah itu, buatlah file zone untuk loid-work.com dan franky-work.com dan arahkan IP secara bebas

Tambahkan konfigurasi squid.conf dengan ``` acl ALLOWED dstdomain "/etc/squid/allowed-sites.acl" ``` dan ``` http_access allow ALLOWED working_1 ```

Restart DNS Server dan Squid dengan command masing-masing ``` service bind9 restart ``` dan ``` service squid restart ```
Ketika kita mengakses loid-work.com atau franky-work.com maka tampilannya akan terlihat seperti ini

![image](https://user-images.githubusercontent.com/72655301/201681364-bc67bcaf-5ff2-4bf3-833a-44dde7b4dac1.png)

Untuk settingan waktu akses hanya pada jam kerja masih belum bisa diselesaikan

## 3.
Agar hanya bisa mengakses website dengan protokol https, maka kita harus melakukan blocking port http, yaitu 80 dan hanya membolehkan akses pada port https, yaitu 443 dengan menambahkan konfigurasi

```
acl Deny_port port 80         # http
acl SSL_port port 443

...

http_access deny Deny_port
http_access deny SSL_port working_1

```

Restart Squid dengan command ``` service squid restart ```
Ketika website dengan http diakses, maka akan muncul tampilan sebagai berikut

![image](https://user-images.githubusercontent.com/72655301/201683251-88341d2d-d834-4d6a-852c-2ce9031029d5.png)


## No. 4
Masih belum dikerjakan
## No. 5
Masih belum dikerjakan

# Kesulitan
Karena masih belum familiar dengan DHCP Server, konfigurasi dilakukan secara perlahan sehingga waktu yang ada terasa kurang. Untuk DHCP Relay tidak ada di modul sehingga masih harus mencari diluar modul. Untuk Squid, konfigurasi yang dilakukan terkadang saling menimpa satu sama lain sehingga beberapa akses yang diperbolehkan dan tidak diperbolehkan tidak berjalan dengan benar.  
