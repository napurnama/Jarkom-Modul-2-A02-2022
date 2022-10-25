# Jarkom-Modul-2-A02-2022
## Topologi
1. Ostania 
3. SSS - 10.0.1.2
4. Garden - 10.0.1.3
5. Berlint - 10.0.2.2
6. Eden - 10.0.2.3
7. WISE - 10.0.3.2

## Praktik

### A Buat Topologi

Buat topologi seperti di [pengenalan GNS3](https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/master/Modul-GNS3#membuat-topologi) kemarin.

Kita akan membuat node `WISE` sebagai DNS server.

### A Instalasi bind

- Buka *WISE* dan update package lists dengan menjalankan command:

	```
	apt-get update
	```

- Setalah melakukan update silahkan install aplikasi bind9 pada *WISE* dengan perintah:

	```
	apt-get install bind9 -y
	```

![instal bind9](images/1.png)

### B Pembuatan Domain
Pada sesilab ini kita akan membuat domain **wise.A02.com**.

- Lakukan perintah pada *WISE*. Isikan seperti berikut:

  ```
  nano /etc/bind/named.conf.local
  ```

- Isikan configurasi domain **wise.A02.com** sesuai dengan syntax berikut:

  ```
  zone "wise.A02.com" {
  	type master;
  	file "/etc/bind/wise/wise.A02.com";
  };
  ```

![config wise.A02.com](images/2-2.png)

- Buat folder **wise** di dalam **/etc/bind**

  ```
  mkdir /etc/bind/wise
  ```

- Copykan file **db.local** pada path **/etc/bind** ke dalam folder **wise** yang baru saja dibuat dan ubah namanya menjadi **wise.A02.com**

  ```
  cp /etc/bind/db.local /etc/bind/wise/wise.A02.com
  ```

- Kemudian buka file **wise.A02.com** dan edit seperti gambar berikut dengan IP *WISE* 10.0.3.2:

  ```
  nano /etc/bind/wise/wise.A02.com
  ```

![konfig wise.A02](images/3-1.png)

- Restart bind9 dengan perintah 

  ```
  service bind9 restart
  
  ATAU
  
  named -g //Bisa digunakan untuk restart sekaligus debugging
  ```



### C Setting nameserver pada client

Domain yang kita buat tidak akan langsung dikenali oleh client oleh sebab itu kita harus merubah settingan nameserver yang ada pada client kita.

- Pada client *SSS* arahkan nameserver menuju IP *WISE* dengan mengedit file _resolv.conf_ dengan mengetikkan perintah 

	```
	nano /etc/resolv.conf
	```

![ping](images/4-1.png)

- Untuk mencoba koneksi DNS, lakukan ping domain **wise.A02.com** dengan melakukan  perintah berikut pada client *SSS*

  ```
  ping wise.A02.com -c 5
  ```

![ping](images/5-1.png)



### D Reverse DNS (Record PTR)

Jika pada pembuatan domain sebelumnya DNS server kita bekerja menerjemahkan string domain **wise.A02.com** kedalam alamat IP agar dapat dibuka, maka Reverse DNS atau Record PTR digunakan untuk menerjemahkan alamat IP ke alamat domain yang sudah diterjemahkan sebelumnya.

- Edit file **/etc/bind/named.conf.local** pada *WISE*

  ```
  nano /etc/bind/named.conf.local
  ```

- Lalu tambahkan konfigurasi berikut ke dalam file **named.conf.local**. Tambahkan reverse dari 3 byte awal dari IP yang ingin dilakukan Reverse DNS. Karena di contoh saya menggunakan IP `10.0.3` untuk IP dari records, maka reversenya adalah `3.0.10` 

  ```
  zone "3.0.10.in-addr.arpa" {
      type master;
      file "/etc/bind/wise/3.0.10.in-addr.arpa";
  };
  ```

![](images/6-1.png)

- Copykan file **db.local** pada path **/etc/bind** ke dalam folder **wise** yang baru saja dibuat dan ubah namanya menjadi **3.0.10.in-addr.arpa**

  ```
  cp /etc/bind/db.local /etc/bind/wise/3.0.10.in-addr.arpa
  ```

  *Keterangan 3.0.10 adalah 3 byte pertama IP WISE yang dibalik urutan penulisannya*

- Edit file **3.0.10.in-addr.arpa** menjadi seperti gambar di bawah ini


![konfig](images/7-1.png)

- Kemudian restart bind9 dengan perintah 

  ```
  service bind9 restart
  ```

- Untuk mengecek apakah konfigurasi sudah benar atau belum, lakukan perintah berikut pada client *SSS* 

  ```
  // Install package dnsutils
  // Pastikan nameserver di /etc/resolv.conf telah dikembalikan sama dengan nameserver dari Foosha
  apt-get update
  apt-get install dnsutils
  
  //Kembalikan nameserver agar tersambung dengan WISE
  host -t PTR "IP WISE"
  ```

![host](images/8-1.png)



### E Record CNAME
Record CNAME adalah sebuah record yang membuat alias name dan mengarahkan domain ke alamat/domain yang lain.

Langkah-langkah membuat record CNAME:

- Buka file **wise.A02.com** pada server *WISE* dan tambahkan konfigurasi seperti pada gambar berikut:


![DNS](images/9-1.png)



- Kemudian restart bind9 dengan perintah

  ```
  service bind9 restart
  ```

- Lalu cek dengan melakukan `host -t CNAME www.wise.A02.com` atau `ping www.wise.A02.com -c 5`. Hasilnya harus mengarah ke host dengan IP *WISE*.


![DNS](images/10-1.png)
