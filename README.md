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


# Proxy
## 1.
## 2.
## 3.
## 4.
## 5.
