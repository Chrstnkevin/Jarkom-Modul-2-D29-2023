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
## Switch 1 (10.36.1.1)
**Wisanggeni** 
````
auto eth0
iface eth0 inet static
	address 10.36.1.2
	netmask 255.255.255.0
	gateway 10.36.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Prabukusuma** 
````
auto eth0
iface eth0 inet static
	address 10.36.1.3
	netmask 255.255.255.0
	gateway 10.36.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Abimanyu** 
````
auto eth0
iface eth0 inet static
	address 10.36.1.4
	netmask 255.255.255.0
	gateway 10.36.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Sadewa** 
````
auto eth0
iface eth0 inet static
	address 10.36.1.5
	netmask 255.255.255.0
	gateway 10.36.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Nakula** 
````
auto eth0
iface eth0 inet static
	address 10.36.1.6
	netmask 255.255.255.0
	gateway 10.36.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
## Switch 2 (10.36.2.1)

**Yudisthira** 
````
auto eth0
iface eth0 inet static
	address 10.36.2.2
	netmask 255.255.255.0
	gateway 10.36.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Werkudara** 
````
auto eth0
iface eth0 inet static
	address 10.36.2.3
	netmask 255.255.255.0
	gateway 10.36.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
**Arjuna** 
````
auto eth0
iface eth0 inet static
	address 10.36.2.4
	netmask 255.255.255.0
	gateway 10.36.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
````
### Ringkasan IP
````
Router	    : 10.36.1.1 (Switch 1)
Wisanggeni  : 10.36.1.2
Prabukusuma : 10.36.1.3
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
Setting client
````
echo -e '
nameserver 10.36.2.2 # IP Yudhistira
nameserver 10.36.2.3 # IP Werkudara
nameserver 192.168.122.1
' > /etc/resolv.conf
apt-get update
apt-get install dnsutils -y
apt-get install lynx -y
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
lalu masukkan perintah ini di client
````
host -t PTR 10.36.1.4
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/3556143f-00ef-4216-9559-e4a6239e6faf)

## Soal No 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
````
nano /etc/bind/named.conf.local
````
tambahkan pada zone arjuna dan abimanyu
````
    	also-notify { 10.36.2.3; } // IP Werkudara
    	allow-transfer { 10.36.2.3; } // IP Werkudara
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/bf587cd1-1e55-46c5-b761-b0e9310a419d)

lalu setting pada **werkudara** pada file /etc/bind/named.conf.local menjadi 
````
zone "arjuna.d29.com" {
    	type slave;
    	masters { 10.36.2.2; }; // IP Yudhistira
    	file "/var/lib/arjuna.d29.com";
};

zone "abimanyu.d29.com" {
    	type slave;
    	masters { 10.36.2.2; }; // IP Yudhistira
    	file "/var/lib/abimanyu.d29.com";
};
````

setelah itu untuk mencoba maka stop bind9
````
service bind9 stop
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/64a9af5b-7b69-47fb-93cf-06f3ef126310)
dan coba ping di ip client dan didapatkan
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/19255f51-ce44-4300-9d2d-c3cf5de8f584)


## Soal No 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Untuk membuat subdomain tersebut kita bisa pergi ke server Yudhistira  dan pergi ko directory ‘/etc/bind/abimanyu.d29/abimanyu.d29.com’ kemudian tambahkan script 
````
ns1     	IN      A       10.36.2.3     ; IP Werkudara
baratayuda  	IN  	NS  	ns1
````
kemudian edit file /etc/bind/named.conf.options
````
nano /etc/bind/named.conf.options
````
Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options
````
allow-query{any;};
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/debc5157-7e28-4ee5-9d06-4c26adb89d35)

setelah itu edit conf file di node **Werkudara** /etc/bind/named.conf.local dengan menambahkan
````
zone "baratayuda.abimanyu.d29.com" {
    	type master;
    	file "/etc/bind/baratayuda/baratayuda.abimanyu.d29.com";
};
````
