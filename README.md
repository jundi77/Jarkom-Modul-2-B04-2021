# Jarkom-Modul-2-B04-2021

Naufal Fajar Imani             05111940000007

Jundullah H. R.                05111940000144

Yeremia D Limantara            05111940000232

------------------------------------------------------------------------------
![image](https://user-images.githubusercontent.com/40772378/139529351-c5f3d141-c0a4-4d78-8bdd-1b2f3cb80313.png)

Diinginkan struktur node-node pada gambar di atas, dengan syarat setiap node dapat terhubung dengan internet. Oleh karenanya dibuat node-node yang kemudian diberi nama Foosha, Loguetown, Alabasta, EniesLobby, Water7, dan Skypie. Setiap node diberikan konfigurasi network seperti berikut pada GNS3 sehingga dapat terkoneksi ke internet melalui node Foosha.

## Konfigurasi Network pada Foosha

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

Adapun kemudian memerlukan setting NAT di Foosha, dilakukan melalui `.bashrc`
```
# NAT settings
echo Applying NAT settings
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.9.0.0/16
```

## Konfigurasi network Loguetown
```
auto eth0
iface eth0 inet static
	address 10.9.1.2
	netmask 255.255.255.0
	gateway 10.9.1.1
```

## Konfigurasi network Alabasta
```
auto eth0
iface eth0 inet static
	address 10.9.1.3
	netmask 255.255.255.0
	gateway 10.9.1.1
```

## Konfigurasi network EniesLobby
```
auto eth0
iface eth0 inet static
	address 10.9.2.2
	netmask 255.255.255.0
	gateway 10.9.2.1
```

## Konfigurasi network Water7
```
auto eth0
iface eth0 inet static
	address 10.9.2.3
	netmask 255.255.255.0
	gateway 10.9.2.1
```

## Konfigurasi network Skypie
```
auto eth0
iface eth0 inet static
	address 10.9.2.4
	netmask 255.255.255.0
	gateway 10.9.2.1
```

Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.B04.com dengan alias www.franky.B04.com pada folder kaizoku.
Setelah itu buat subdomain super.franky.B04.com dengan alias www.super.franky.B04.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
Buat juga reverse domain untuk domain utama

## Konfigurasi DNS

Menambahkan DNS master dan slave di Loguetown dan Alabasta
```
# Nameserver Config
echo nameserver 10.9.2.2 > /etc/resolv.conf # EniesLobby
echo nameserver 10.9.2.3 >> /etc/resolv.conf # Water7
echo nameserver 192.168.122.1 >> /etc/resolv.conf
```

### Konfigurasi DNS Master di EniesLobby

`/etc/bind/named.conf.local` EniesLobby
```
zone "franky.B04.com" {
        type master;
        notify yes;
        also-notify { 10.9.2.3; };
        allow-transfer { 10.9.2.3; };
        file "/etc/bind/kaizoku/franky.B04.com";
};

zone "2.9.10.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.9.10.in-addr.arpa";
};
```
`/etc/bind/kaizoku/2.9.10.in-addr.arpa` EniesLobby
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                     2021100401         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.9.10.in-addr.arpa.    IN      NS      franky.B04.com.
4       IN      PTR     franky.B04.com.
;@      IN      AAAA    ::1
```

`/etc/bind/kaizoku/franky.B04.com` EniesLobby
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.B04.com. root.franky.B04.com. (
                     2021100401         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.B04.com.
@       IN      A       10.9.2.4        ; IP Skypie
www       IN      CNAME      franky.B04.com.
super       IN      A       10.9.2.4        ; IP Skypie
www.super       IN      CNAME      super.franky.B04.com.
ns1     IN      A       10.9.2.3
mecha   IN      NS      ns1
```
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama
Setelah itu terdapat subdomain mecha.franky.B04.com dengan alias www.mecha.franky.B04.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.B04.com dengan alias www.general.mecha.franky.B04.com yang mengarah ke Skypie

### Konfigurasi DNS Slave di Water7

`/etc/bind/named.conf.local` Water7
```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "franky.B04.com" {
    type slave;
    masters { 10.9.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.B04.com";
};

zone "mecha.franky.B04.com" {
    type master;
    file "/etc/bind/sunnygo/mecha.franky.B04.com";
};
```

`/etc/bind/sunnygo/mecha.franky.B04.com` Water7
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.B04.com. root.mecha.franky.B04.com. (
                     2021102510         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.B04.com.
@       IN      A       10.9.2.4
www     IN      CNAME   mecha.franky.B04.com.
general       IN      A       10.9.2.4
www.general     IN      CNAME   general.mecha.franky.B04.com.
```

## Konfigurasi Webserver di Skypie

Setelah mengunduh requirement pada [link berikut](https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom), dilakukan `unzip` dan rename untuk tiap-tiap folder:
- franky -> franky.B04.com
- general.mecha.franky -> general.mecha.franky.B04
- super.franky -> super.franky.B04.com

Kemudian untuk tiap-tiap folder requirement dipindahkan ke `/var/www`.

Untuk auth basic luffy yang dijelaskan di nomor 15, dibuat sesuai tutorial modul dan diletakkan di `/etc/apache2/.htpasswd`, dengan isi:
```
luffy:$apr1$oWUMSnpM$aD.xymZufijsyuMFlpxo10
```

Selanjutnya adalah membuat konfigurasi apache2 di Skypie, adapun filenya `/etc/apache2/sites-enabled/000-default.conf` dengan isi seperti berikut:
```
ServerName franky.B04.com

<VirtualHost *:80>
    ServerName  10.9.2.4
    Redirect    301 /   http://www.franky.B04.com
</VirtualHost>


<VirtualHost *:80>
    ServerName franky.B04.com
    ServerAlias www.franky.B04.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/franky.B04.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/franky.B04.com>
        RewriteEngine on
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule (.*) /index.php/$1 [L]
    </Directory>
</VirtualHost>

<VirtualHost *:80>
    ServerName super.franky.B04.com
    ServerAlias www.super.franky.B04.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/super.franky.B04.com
    ErrorDocument 404 /error/404.html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Alias "/js" "/var/www/super.franky.B04.com/public/js"

    <Directory /var/www/super.franky.B04.com/public/images>
        Options +Indexes

        RewriteEngine on
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule (.*)(franky)+(.*) franky.png [L]
    </Directory>

    <Directory /var/www/super.franky.B04.com/public>
        Options +Indexes
    </Directory>
</VirtualHost>

<VirtualHost *:15000 *:15500>
    ServerName general.mecha.franky.B04.com
    ServerAlias www.general.mecha.franky.B04.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/general.mecha.franky.B04
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory "/var/www/general.mecha.franky.B04">
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
  </Directory>
</VirtualHost>
```
ServerName menyesuaikan domain di DNS, dan untuk domain alias maka diletakkan konfigurasinya di ServerAlias.

### VirtualHost dengan ServerName IP Skypie
Isinya langsung melakukan redirect ke www.franky.B04.com, sehingga setiap akses ke IP Skypie langsung diarahkan ke www.franky.B04.com.

### VirtualHost dengan ServerName franky.B04.com
Isinya mengarah ke directory `/var/www/franky.B04.com`. Kemudian supaya akses menuju `/home` diarahkan secara internal ke `/index.php/home`, dilakukan rewrite dengan menggunakan modul apache rewrite untuk direktori `/var/www/franky.B04.com`. Dengan config rewrite yang diberikan, seluruh akses ke `/var/www/franky.B04.com` yang file/direktorinya tidak ada di sana, akan diarahkan ke index.php.

### VirtualHost dengan ServerName super.franky.B04.com
Isinya mengarah ke directory `/var/www/super.franky.B04.com`. Terdapat alias untuk route `/js` agar diarahkan ke `/var/www/super.franky.B04.com/public/js` secara internal. Kemudian untuk direktori `/var/www/super.franky.B04.com/public` diberikan config `Options +Indexes` sehingga dapat dilakukan listing direktori. Untuk `/var/www/super.franky.B04.com/public/images`, ditambahkan config rewrite sehingga segala request yang mengarah ke file dengan substring `franky`, akan diarahkan secara internal ke `franky.png`. Untuk penggantian halaman default 404 dilakukan dengan `ErrorDocument 404 /error/404.html`.

### VirtualHost dengan ServerName general.mecha.franky.B04.com
Isinya mengarah ke `/var/www/general.mecha.franky.B04`, dengan akses dibatasi untuk port 15000 dan 15500 saja. Di sini dilakukan konfigurasi auth basic dengan file `.htpasswd` diletakkan di `/etc/apache2/.htpasswd`, sehingga setiap akses ke sini perlu login dengan user luffy terlebih dahulu.

## Kendala
1. Lupa bahwa saat ada penggunaan rewrite di apache2, harus disertakan `RewriteEngine on` sebelumnya. Ini cukup menguras waktu karena sekilas config terlihat benar urutan dan syaratnya, namun karena tidak yakin maka config yang justru diubah2 dan didebug, bukan penulisan `RewriteEngine on`-nya.
