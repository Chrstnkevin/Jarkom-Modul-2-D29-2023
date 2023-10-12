# Jarkom-Modul-2-D29-2023
## Soal No 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 
Kami mendapatkan topologi 8 seperti berikut:
![08](https://github.com/Chrstnkevin/UFO-2022-Alien-Escape/assets/97864068/54364057-1a61-4d65-aaf5-d1d50595396b)

lalu dibuatlah 8 topologi sesuai perintah
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/02ab7892-20b1-4851-85b1-11403a76aad7)

#### ganti konfigurasi  
**Router**
````
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.36.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.36.2.1
	netmask 255.255.255.0
````

**Yudisthira** 
````
auto eth0
iface eth0 inet static
	address 10.36.2.2
	netmask 255.255.255.0
	gateway 10.36.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````

````
Router	    : 10.36.1.1 (Switch 1)
Wisanggeni	: 10.36.1.2
Prabukusuma	: 10.36.1.3
Abimanyu    : 10.36.1.4
Sadewa 	    : 10.36.1.5
Nakula 	    : 10.36.1.6

Router	    : 10.36.2.1 (Switch 2)
Yudhistira  : 10.36.2.2
Werkudara   : 10.36.2.3
Arjuna      : 10.36.2.4
````
pada router ketik
````
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.36.0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
````

Pada node lain masukkan
````
echo nameserver 192.168.122.1 > /etc/resolv.conf
````

## Soal No 2 dan 3
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok. Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
##### Setting Yudhistira sebagai DNSMaster
Masukkan ini di Yudisthira
````
 apt-get update
 apt-get install bind9 -y
````

Setelah itu Konfigurasi file
````
nano /etc/bind/named.conf.local
````

````
zone "arjuna.d29.com" {
        type master;
        file "/etc/bind/arjuna.d29/arjuna.d29.com";
};

zone "abimanyu.d29.com" {
        type master;
        file "/etc/bind/abimanyu.d29/abimanyu.d29.com";
};
````
##### Membuat website utama dengan akses arjuna.yyy.com
Pada "Yudhistira" buat folder di dalam etc/bind
````
mkdir /etc/bind/arjuna.d29
````
Copy file db.local dan ganti nama
````
cp /etc/bind/db.local /etc/bind/arjuna.d29/arjuna.d29.com
````

Buka file /etc/bind/arjuna.d29/arjuna.d29.com dan tuliskan
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     d29.com. root.d29.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.d29.com.
@       IN      A       10.36.2.4       ; IP Arjuna 
www     IN      CNAME   arjuna.d29.com. ; Alias
@       IN      AAAA    ::1
````

##### Membuat website utama dengan akses abimanyu.yyy.com
Copy file db.local dan ganti nama
````
cp /etc/bind/db.local /etc/bind/abimanyu.d29/abimanyu.d29.com
````

Buka file /etc/bind/abimanyu.d29/abimanyu.d29.com dan tuliskan
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     d29.com. root.d29.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.d29.com.
@       IN      A       10.36.1.4         ; IP Abimanyu 
www     IN      CNAME   abimanyu.d29.com. ; Alias
@       IN      AAAA    ::1
````

Restart
````
service bind9 restart
````
dan berikut hasilnya
![image](https://github.com/Chrstnkevin/UFO-2022-Alien-Escape/assets/97864068/98414ddd-239e-473a-b571-00ce5619a75e)
![image](https://github.com/Chrstnkevin/UFO-2022-Alien-Escape/assets/97864068/51308635-4ffd-4119-adca-340c55f5fb5c)
![image](https://github.com/Chrstnkevin/UFO-2022-Alien-Escape/assets/97864068/bfa8809b-c6d1-4452-9d44-4207efb12bb8)

## Soal No 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
##### Membuat Subdomain "parikesit.abimanyu.yyy.com"
Buka file /etc/bind/abimanyu.d29/abimanyu.d29.com pada Yudhistira
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     d29.com. root.d29.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      abimanyu.d29.com.
@             IN      A       10.36.1.4                   ; IP Abimanyu 
www           IN      CNAME   abimanyu.d29.com.           ; Alias
parikesit     IN      A	      10.36.1.4			  ; IP Abimanyu
@             IN      AAAA    ::1
````

Restart
````
service bind9 restart
````
dan coba ping untuk melihat apakah berhasil atau tidak
![image](https://github.com/Chrstnkevin/UFO-2022-Alien-Escape/assets/97864068/919d972b-3918-4828-a1d6-4dcf8a503f13)

## Soal No 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
buka terlebih dahulu file etc/bind/named.conf.local
````
nano /etc/bind/named.conf.local
````

masukkan didalam file tersebut
````
zone "1.36.10.in-addr.arpa" { 
    type master;
    file "/etc/bind/1.36.10.in-addr/1.36.10.in-addr.arpa";
};
````

Coppy filenya
````
cp /etc/bind/db.local /etc/bind/1.36.10.in-addr/1.36.10.in-addr.arpa
````

Buka dan edit file /etc/bind/1.36.10.in-addr/1.36.10.in-addr.arpamenjadi seperti ini
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     d29.com. root.d29.com. (
			2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.36.10.in-addr.arpa.   IN      NS      abimanyu.d29.com.
4                       IN      PTR     abimanyu.d29.com. ; Byte ke-4 abimanyu
````
lalu restart bind9
````
service bind9 restart
````

