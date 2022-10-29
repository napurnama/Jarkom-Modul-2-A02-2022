# Jarkom Modul 2 2022
Kelompok A02
+ Ferdinand Putra Gumilang Silalahi - 5025201176
+ Naufal Adli Purnama - 5025201195
+ Bimantara Tito Wahyudi - 5025201227

## Soal
1. WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet.

### Dokumentasi Topologi
### Dokumentasi network configuration WISE
### Dokumentasi WISE Ping Google.com

2. Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.A02.com dengan alias www.wise.A02.com pada folder wise. 

+ **WISE**
	script:
	```
	apt-get -y install bind9
	cp ~/no1/named.conf.local /etc/bind/named.conf.local
	mkdir /etc/bind/wise
	cp ~/no1/wise.A02.com /etc/bind/wise/wise.A02.com
	service bind9 restart
	```

	isi ~/no1/named.conf.local:
	```
	zone "wise.A02.com" {
		type master;
		file "/etc/bind/wise/wise.A02.com";
	};
	```

	isi ~/no1/wise.A02.com:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     wise.A02.com. root.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	@       IN      NS      wise.A02.com.
	@       IN      A       10.0.2.2
	www     IN      CNAME   wise.A02.com.
	@       IN      AAAA    ::1
	```

3. Setelah itu ia juga ingin membuat subdomain eden.wise.A02.com dengan alias www.eden.wise.A02.com yang diatur DNS-nya di WISE dan mengarah ke Eden

+ **WISE**
	script:
	```
	cp ~/no2/wise.A02.com /etc/bind/wise/wise.A02.com
	service bind9 restart
	```

	isi ~/no2/wise.A02.com:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     wise.A02.com. root.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	@       				IN      NS      wise.A02.com.
	@       				IN      A       10.0.2.2
	www     				IN      CNAME   wise.A02.com.
	eden    				IN      A       10.0.3.3
	www.eden        IN      CNAME   eden.wise.A02.com.
	@       				IN      AAAA    ::1
	```

4. Buat juga reverse domain untuk domain utama

+ **WISE**
	script:
	```
	cp ~/no3/named.conf.local /etc/bind/named.conf.local
	cp ~/no3/2.0.10.in-addr.arpa /etc/bind/wise/2.0.10.in-addr.arpa
	service bind9 restart
	```

	isi ~/no3/named.conf.local:
	```
	zone "wise.A02.com" {
		type master;
		file "/etc/bind/wise/wise.A02.com";
	};

	zone "2.0.10.in-addr.arpa" {
		type master;
		file "/etc/bind/wise/2.0.10.in-addr.arpa";
	};
	```

	isi ~/no3/2.0.10.in-addr.arpa:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     wise.A02.com. root.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	2.0.10.in-addr.arpa.    IN      NS      wise.A02.com.
	2                       IN      PTR     wise.A02.com.
	```

5. Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama

+ **WISE**
	script:
	```
	cp ~/no4/named.conf.local /etc/bind/named.conf.local
	service bind9 restart
	```

	isi ~/no4/named.conf.local:
	```
	zone "wise.A02.com" {
		type master;
		file "/etc/bind/wise/wise.A02.com";
		notify yes;
		also-notify { 10.0.3.2; };
		allow-transfer { 10.0.3.2; };
	};

	zone "2.0.10.in-addr.arpa" {
		type master;
		file "/etc/bind/wise/2.0.10.in-addr.arpa";
	};
	```

+ **Berlint**
	script:
	```
	apt-get install -y bind9
	cp ~/no4/named.conf.local /etc/bind/named.conf.local
	service bind9 restart
	```

	isi ~/no4/named.conf.local:
	```
	zone "wise.A02.com" {
		type slave;
		masters { 10.0.2.2; };
		file "/var/lib/bind/wise.A02.com";
	};
	```

6. Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.A02.com dengan alias www.operation.wise.A02.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation
+ WISE
	script:
	```
	cp ~/no5/wise.A02.com /etc/bind/wise/wise.A02.com
	cp ~/no5/named.conf.options /etc/bind/named.conf.options
	service bind9 restart
	```

	isi ~/no5/wise.A02.com:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     wise.A02.com. root.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	@       				IN      NS      wise.A02.com.
	@       				IN      A       10.0.2.2
	www     				IN      CNAME   wise.A02.com.
	eden    				IN      A       10.0.3.3
	www.eden        IN      CNAME   eden.wise.A02.com.
	ns1     				IN      A       10.0.3.2
	operation       IN      NS      ns1
	@       				IN      AAAA    ::1
	```

	isi ~/no5/named.conf.options:
	```
	options {
		directory "/var/cache/bind";

		//dnssec-validation auto;
		allow-query{ any; };

		auth-nxdomain no;    # conform to RFC1035
		listen-on-v6 { any; };
	};
	```

+ Berlint
	script:
	```
	cp ~/no5/named.conf.options /etc/bind/named.conf.options
	cp ~/no5/named.conf.local /etc/bind/named.conf.local
	mkdir /etc/bind/operation/operation.wise.A02.com
	cp ~/no5/operation.wise.A02.com /etc/bind/operation/operation.wise.A02.com
	service bind9 restart
	```

	isi ~/no5/named.conf.options:
	```
	zone "wise.A02.com" {
		type slave;
		masters { 10.0.2.2; };
		file "/var/lib/bind/wise.A02.com";
	};

	zone "operation.wise.A02.com" {
		type master;
		file "/etc/bind/operation/operation.wise.A02.com";
	};
	```

	isi ~/no5/named.conf.local:
	```
	options {
		directory "/var/cache/bind";

		//dnssec-validation auto;
		allow-query{ any; };

		auth-nxdomain no;    # conform to RFC1035
		listen-on-v6 { any; };
	};
	```

	isi ~/no5/operation.wise.A02.com:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     operation.wise.A02.com. root.operation.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	@       IN      NS      operation.wise.A02.com.
	@       IN      A       10.0.3.3
	www     IN      CNAME   operation.wise.A02.com.
	```

7. Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.A02.com dengan alias www.strix.operation.wise.A02.com yang mengarah ke Eden
+ Berlint
	script:
	```
	cp ~/no6/operation.wise.A02.com /etc/bind/operation/operation.wise.A02.com
	service bind9 restart
	```

	isi ~/no6/operation.wise.A02.com:
	```
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     operation.wise.A02.com. root.operation.wise.A02.com. (
				 2022102601         ; Serial
						 604800         ; Refresh
							86400         ; Retry
						2419200         ; Expire
						 604800 )       ; Negative Cache TTL
	;
	@       				IN      NS      operation.wise.A02.com.
	@       				IN      A       10.0.3.3
	www     				IN      CNAME   operation.wise.A02.com.
	strix   				IN      A       10.0.3.3
	www.strix       IN      CNAME   strix.operation.wise.A02.com.
	```

8. Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.A02.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.A02.com
+ WISE
	script:
	```
	apt-get install -y apache2
	cp ~/no7/000-default.conf /etc/apache2/sites-available/000-default.conf
	mkdir /var/www/wise.A02.com
	service apache2 start
	```
	
	000-default.conf
	```
	<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.A02.com
        ServerName wise.A02.com
        ServerAlias www.wise.A02.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

	</VirtualHost>

	```


9. Setelah itu, Loid juga membutuhkan agar url www.wise.A02.com/index.php/home dapat menjadi menjadi www.wise.A02.com/home

+ WISE
	script:
	```
	apt-get install libapache2-mod-php7.0
	cp ~/no8/000-default.conf /etc/apache2/sites-available/000-default.conf
	cp ~/res/wise/* /var/www/wise.A02.com/
	service apache2 restart
	```
	
	000-default.conf
	```
	<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.A02.com
        ServerName wise.A02.com
        ServerAlias www.wise.A02.com

        Alias "/home" "/var/www/wise.A02.com/index.php"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

	</VirtualHost>
	```
	

10. Setelah itu, pada subdomain www.eden.wise.A02.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.A02.com

11. Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja

12. Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

13. Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.A02.com/public/js menjadi www.eden.wise.A02.com/js

14. Loid meminta agar www.strix.operation.wise.A02.com hanya bisa diakses dengan port 15000 dan port 15500

15. dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.A02

16. dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.A02.com

17. Karena website www.eden.wise.A02.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian!


## Kendala
Kendala ketika pengerjaan praktikum adalah penyimpanan perintah-perintah yang dijalankan. Terjadi beberapa kesalahan sehingga harus diulang dari awal karena penyimpanan perintah dah file konfigurasi tidak tepat. Selain itu, perlu ketelitian dalam pengerjaan dan kejelian dalam proses debugging yang cukup memakan waktu. Terkadang terdapat masalah jaringan pada GNS3 sehingga bug yang ada bukan dari kesalahan penulisan, tetapi dari koneksi internet. Untuk itu, teman kami Bima menemukan cara, yaitu dengan menjalankan perintah ping agar dapat memantau koneksi internet.
