# Jarkom-Modul-2-D29-2023
| Nama           | NRP            |
| ---------------| ---------------|
| Christian Kevin Emor      | 5025211153      |
| Ferza Noveri     | 5025211097      |

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
> Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok. Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

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
        file "/etc/bind/arjuna/arjuna.d29.com";
};

zone "abimanyu.d29.com" {
        type master;
        file "/etc/bind/abimanyu/abimanyu.d29.com";
};
````
##### Membuat website utama dengan akses arjuna.yyy.com
Pada "Yudhistira" buat folder di dalam etc/bind
````
mkdir /etc/bind/arjuna
````
Copy file db.local dan ganti nama
````
cp /etc/bind/db.local /etc/bind/arjuna/arjuna.d29.com
````

Buka file /etc/bind/arjuna/arjuna.d29.com dan tuliskan
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     arjuna.d29.com. root.arjuna.d29.com. (
                        2	        ; Serial
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
cp /etc/bind/db.local /etc/bind/abimanyu/abimanyu.d29.com
````

Buka file /etc/bind/abimanyu/abimanyu.d29.com dan tuliskan
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     abimanyu.d29.com. root.abimanyu.d29.com. (
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
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/3fd1eb25-d08d-46c0-b920-4c0ca437469a)

## Soal No 4
> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
##### Membuat Subdomain "parikesit.abimanyu.yyy.com"
Buka file /etc/bind/abimanyu/abimanyu.d29.com pada Yudhistira
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
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/bd4580c8-ef7a-42d5-a713-e434e37bbd46)

## Soal No 5
> Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

buka terlebih dahulu file etc/bind/named.conf.local
````
nano /etc/bind/named.conf.local
````

masukkan didalam file tersebut
````
zone "1.36.10.in-addr.arpa" { 
    type master;
    file "/etc/bind/abimanyu/1.36.10.in-addr.arpa";
};
````

Coppy filenya
````
cp /etc/bind/db.local /etc/bind/abimanyu/1.36.10.in-addr.arpa
````

Buka dan edit file /etc/bind/abimanyu/1.36.10.in-addr.arpamenjadi seperti ini
````
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     abimanyu.d29.com. root.abimanyu.d29.com. (
			2	        ; Serial
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
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/0913fdb1-42ff-47f3-a89b-4dcd6eb48d27)

## Soal No 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
````
nano /etc/bind/named.conf.local
````
tambahkan pada node **Yudhistira** zone arjuna dan abimanyu
````
	notify yes;
	also-notify { 10.36.2.3; } // IP Werkudara
    	allow-transfer { 10.36.2.3; } // IP Werkudara
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/3bfa5ab3-a998-4807-a000-59ad0da88888)

lalu setting pada node **werkudara** pada file /etc/bind/named.conf.local menjadi 
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
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/6e549651-f39d-43ff-bc8b-e716a3b3605a)

dan coba ping di ip client dan didapatkan
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/21d3e9c5-8259-4d63-8a39-9c19bc0f0754)

## Soal No 7
> Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Untuk membuat subdomain tersebut kita bisa pergi ke server **Yudhistira**  dan pergi ko directory ‘/etc/bind/abimanyu/abimanyu.d29.com’ kemudian tambahkan script 
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
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/4407be00-6198-4a19-b7e6-a7a43d94f998)

setelah itu edit conf file di node **Werkudara** /etc/bind/named.conf.local dengan menambahkan
````
zone "baratayuda.abimanyu.d29.com" {
    	type master;
    	file "/etc/bind/baratayuda/baratayuda.abimanyu.d29.com";
};
````
buat folder delegasi
````
mkdir /etc/bind/delegasi
````
dan copy file
````
cp /etc/bind/db.local /etc/bind/delegasi/baratayuda.abimanyu.d29.com
````
masukkan di /etc/bind/baratayuda/baratayuda.abimanyu.d29.com
````
;
; BIND data file for local loopback interface
;
$TTL	604800
@   	IN  	SOA 	abimanyu.d29.com. root.abimanyu.d29.com. (
                    	2023101001  	; Serial
                     	604800     	; Refresh
                      	86400     	; Retry
                    	2419200     	; Expire
                     	604800 )   	; Negative Cache TTL
;
@   	IN  	NS  	baratayuda.abimanyu.d29.com.
@   	IN  	A   	10.36.1.4   	; IP Abimanyu
www 	IN  	CNAME   baratayuda.abimanyu.d29.com.
````
lalu kita coba ping baratayuda.abimanyu.d29.com melalui client dan berhasil
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/936b6f31-d5bf-4a2d-9a09-20f2651b70aa)

## Soal No 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

pada node **Werkudara** kita lakukan delegasi dengan cara
````
rjp             IN      A       10.36.1.4     ; IP Abimanyu
www.rjp         IN      CNAME   rjp.baratayuda.abimanyu.d29.com.
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/7e9baf9d-c1a8-4977-8c5f-5916dc8d69c5)

dan lakukan testing melalui client di rjp.baratayuda.abimanyu.d29.com lalu didapatkan hasil
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/8c4cccf5-c1f7-4600-b365-f84a20ee6b94)

## Soal No 9
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai web server) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Pada Node PrabukusumaWebServer, AbimanyuWebServer, WisanggeniWebServer masukkan:  
````
echo nameserver 192.168.122.1 > /etc/resolv.conf


apt-get update && apt install nginx php php-fpm -y


# Buat folder jarkom
mkdir /var/www/jarkom


# echo ke file /var/www/jarkom/index.php
echo '<?php
echo "Hello World from prabukusuma";
?>' > /var/www/jarkom/index.php


echo '
 server {


        listen 80;


        root /var/www/jarkom;


        index index.php index.html index.htm;
        server_name _;


        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }


        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }


 location ~ /\.ht {
                        deny all;
        }


        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom


ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.0-fpm stop
service php7.0-fpm start

````
### Pada node ArjunaLoadBalancer masukkan:
````
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update

# Install bind9
apt-get install bind9 nginx -y

echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 10.36.1.4 #IP Abimanyu
  server 10.36.1.5 #IP Prabukusuma
  server 10.36.1.6 #IP Wisanggeni
 }

 server {
  listen 80;
  server_name arjuna.d29.com www.arjuna.d29.com;


  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-arjuna


ln -s /etc/nginx/sites-available/lb-arjuna /etc/nginx/sites-enabled/lb-arjuna

service nginx restart

````

## Soal No 10
> Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
> - Prabakusuma:8001
> - Abimanyu:8002
> - Wisanggeni:8003

### Ubah konfigurasi file /etc/nginx/sites-available/jarkom pada node PrabukusumaWebServer, AbimanyuWebServer, WisanggeniWebServer
dengan menambahkan di server port yang dituju
````
listen 8001; # untuk Abimanyu
listen 8002; # untuk Prabukusuma
listen 8003; # untuk Wisanggeni

service nginx restart
````
/etc/nginx/sites-available/lb-arjuna
### Ubah konfigurasi file /etc/nginx/sites-available/lb-arjuna pada node ArjunaLoadBalancer dan tambahkan

````
# Default menggunakan Round Robin
 upstream myweb  {
  server 10.36.1.4:8001; #IP Abimanyu
  server 10.36.1.5:8002; #IP Prabukusuma
  server 10.36.1.6:8003; #IP Wisanggeni
 }

 server {
  listen 80;
  server_name arjuna.d29.com www.arjuna.d29.com;


  location / {
  proxy_pass http://myweb;
  }
}
````

lalu simpan, kemudian buat symlink
````
ln -s /etc/nginx/sites-available/lb-arjuna /etc/nginx/sites-enabled/lb-arjuna
````
terakhir restart Nginx
````
service nginx restart
````
dan coba cek pada client dengan melakukan
````
lynx arjuna.d29.com.
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/399940de-73c6-46d7-9b63-009b8130ffb4)

## Soal No 11
> Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

Melakukan instalasi semua komponen yang diperlukan dengan perintah berikut:
````
apt-get install apache2
apt-get install wget
apt-get install unzip
````
Mengimpor file dari Google Drive untuk mengisi direktori /var/www dengan menggunakan perintah wget:
````
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc' -O abi
````

Menjalankan perintah unzip untuk mengekstrak isi file "abi" ke dalam direktori "abimanyu.d29" di dalam "/var/www":
````
unzip abi -d /var/www/abimanyu.a07
````
Menghapus file "abi" setelah diekstrak:
````
rm abi
````
Memindahkan semua isi direktori "abimanyu.d29/abimanyu.yyy.com" ke direktori "abimanyu.d29":
````
mv /var/www/abimanyu.a07/abimanyu.yyy.com/* /var/www/abimanyu.a07
````
Menghapus direktori "abimanyu.d29/abimanyu.yyy.com" yang sudah tidak diperlukan:
````
rmdir /var/www/abimanyu.a07/abimanyu.yyy.com
````
Membuat file konfigurasi "abimanyu.d29.conf" di dalam direktori "/etc/apache2/sites-available" dan menyalin isi dari file "000-default.conf" ke dalamnya:
````
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.d29.conf
````
Di dalam tag "VirtualHost" pada file "abimanyu.d29.conf", mengkonfigurasi nama server, alias server, admin server, dan mengatur "DocumentRoot" ke "/var/www/abimanyu.d29" yang sudah diisi sebelumnya.
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/db6932e9-722d-4233-a6bb-5e4d66ebd22c)

Hasil dari akses menggunakan 
````
lynx http://www.abimanyu.d29.com/index.php/home" 
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/490dddd8-5e5d-4bb3-aa1c-16a4e056f698)

## Soal No 12
> Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
````
nano /etc/apache2/sites-available/abimanyu.d29.conf
````
Tambahkan konfigurasi seperti berikut di directory
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/5f567ace-da50-499a-a909-ab7deca2e76a)

lalu buka di client
````
lynx http://www.abimanyu.d29.com/home" 
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/490dddd8-5e5d-4bb3-aa1c-16a4e056f698)

## Soal No 13
> Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
Mengimpor file dari Google Drive untuk mengisi direktori /var/www dengan menggunakan perintah wget:
````
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS' -O pari
````

Menjalankan perintah unzip untuk mengekstrak isi file "abi" ke dalam direktori "abimanyu.d29" di dalam "/var/www":
````
unzip pari -d parikesit.abimanyu.d29
rm pari
mv parikesit.abimanyu.d29/parikesit.abimanyu.yyy.com/* parikesit.abimanyu.d29
rmdir parikesit.abimanyu.d29/parikesit.abimanyu.yyy.com
````
copy file ke parikesit
````
cp 000-default.conf parikesit.abimanyu.d29.conf
````
Di dalam tag "VirtualHost" pada file "parikesit.abimanyu.d29.conf", mengkonfigurasi nama server, alias server, admin server, dan mengatur "DocumentRoot" ke "/var/www/parikesit.abimanyu.d29" yang sudah diisi sebelumnya.
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/fff67371-beac-4f69-a1f9-07f43f6f0145)


Hasil dari akses menggunakan 
````
lynx http://www.parikesit.abimanyu.d29.com/
````
![image](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/3087d54a-f499-4959-a381-429c77b97dbd)

## Soal No 14
> Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

````
nano /etc/apache2/sites-available/parikesit.abimanyu.d29.conf
````
lalu tambahkan
````
<Directory /var/www/parikesit.abimanyu.d29/public
	Options +Indexes
</Directory>

<Directory /var/www/parikesit.abimanyu.d29/secret
	Options -Indexes
</Directory>
````
didapatkan hasil
````
lynx parikesit.abimanyu.d29.com/public
````
![Screenshot (180)](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/c23128eb-83a2-4447-944d-8c2c333e4991)

````
lynx parikesit.abimanyu.d29.com/secret
````
![Screenshot (179)](https://github.com/Chrstnkevin/Jarkom-Modul-2-D29-2023/assets/97864068/1d226030-faef-4802-bba2-50403c448094)

## Soal No 15 - 20
> Tidak Berhasil Terkerja


