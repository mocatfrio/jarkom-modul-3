# 1. Dynamic Host Configuration Protocol (DHCP)
## 1.1 Konsep Dasar
### 1.1.1 Pengertian

**DHCP (Dynamic Host Configuration Protocol)** adalah protokol yang berbasis arsitektur client/server yang dipakai untuk memudahkan pengalokasian alamat IP dalam satu jaringan. DHCP secara otomatis meminjamkan nomor IP kepada komputer yang memintanya. 

![Cara Kerja DHCP](https://github.com/mocatfrio/Jarkom-Modul-3/blob/master/DHCP%20Server/images/cara-kerja.png)

Sebuah jaringan lokal yang tidak menggunakan DHCP harus memberikan alamat IP kepada semua komputer secara manual. Namun jika DHCP dipasang di jaringan lokal, maka semua komputer yang tersambung di jaringan akan mendapatkan alamat IP secara otomatis dari DHCP server. Selain alamat IP, banyak parameter lain yang diberikan oleh DHCP, seperti default gateway dan DNS server.

### 1.1.2 Jenis
1. DHCP Client



### 1.1.2 Cara Kerja

![Cara Kerja DHCP](https://github.com/mocatfrio/Jarkom-Modul-3/blob/master/DHCP%20Server/images/dhcp.png)

Terdapat 4 tahapan yang dilakukan dalam proses peminjaman alamat IP pada DHCP:

1. **IP Least Request**: Client meminta alamat IP yang tersedia ke DHCP server (broadcast mencari DHCP server).

2. **IP Least Offer**: DHCP server menjawab dengan memberikan penawaran alamat IP ke client tersebut.

3. **IP Lease Selection**: Client memilih penawaran DHCP Server yang pertama diterima dan kembali melakukan broadcast menyetujui peminjaman alamat IP tersebut kepada DHCP Server.

4. **IP Lease Acknowledge**: DHCP Server memberikan jawaban atas pesan tersebut berupa konfirmasi alamat IP dan informasi lain kepada client dengan sebuah ACKnowledgment. Setelah server meminjamkan (lease) alamat IP, server juga mencoret alamat IP tersebut dari daftar pool (alamat IP yang tersedia). Client melakukan inisialisasi dengan mengikat (binding) alamat IP tersebut.

Sumber Dari: http://www.begal-tech.com/2015/06/pengertian-cara-kerja-dhcp-server-keuntungan-kerugian.html#ixzz5BO26nRYC
 

### 1.1.3 DHCP Message

1. **DHCPDISCOVER**

    Ini merupakan tipe pertama dari DHCP, yang menentukan klien broadcast untuk menemukan server DHCP lokal. Opsi Message Type dikodekan ‘1’.

2. **DHCPOFFER**

    DHCP Server yang menerima satu klien DHCPDISCOVER dan yang dapat melayani permintaan operasi, mengirim DHCPOFFER pada klien dengan sekumpulan parameter. Opsi Messsage Type dikodekan ‘2’.

3. **DHCPREQUEST**

    Klien menerima satu atau lebih DHCPOFFER dan memutuskan tawaran yang diterima. Klien kemudian mengirim tawaran DHCPREQUEST ke “pemenang”. Semua server yang lain mengetahui pesan broadcast ini dan dapat memutuskan bahwa mereka “kalah". Opsi Message Type dikodekan ‘3’.

4. **DHCPACK**

    Akhirnya server mengirim DHCPACK ke klien dengan sekumpulan parameter konfigurasi, mengkonfirmasi pada klien bahwa DHCPREQUEST diterima, dan memberikan kumpulan informasi yang diperlukan. Bagian ACK dari nama pesan ini kependekan dari “acknowledge”. Opsi Message Type dikodekan ‘5’

5. **DHCPNACK**

    Jika klien meminta (dengan pesan DHCPREQUEST) alamat yang salah, kadaluwarsa, atau yang lainnya yang tidak dapat diterima, maka server mengirim DHCPNAK keklien untuk memberitahu bahwa ia tidak dapat memperoleh alamat tersebut. ‘NAK” dalam hal ini kependekan dari “negative acknowledge”. Opsi Message Type dikodekan ‘5’

6. **DHCPDECLINE**

    Jika klien menerima alamat yang diminta, dan secara berturutan menemukan bahwa alamat itu telah digunakan ditempat lain dalam jaringan, ia harus mengirim DHCPDECLINE ke server. Klien mungkin mencoba mengirim suara ke alamat. Jika ada jawaban berarti ada orang yang menggunakan alamat server. Opsi Message Type dikodekan ‘4’

7. **DHCPRELEASE**

    Jika klien tidak lagi perlu menggunakan alamat yang ditunjuk secara dinamis, ia harus mengirim pesan DHCPRELEASE ke server supaya server mengetahui bahwa alamat tidak lagi digunakan. Tidak semua klien DHCP melakukan hal ini karena merupakan pilihan teknis. Opsi Message Type dikodekan ‘7’

8. **DHCPINFORM**

    Jika klien telah mempunyai alamat IP, tetapi masih memerlukan beberapa informasi konfigurasi, maka pesan DHCPINFORM akan melayani tugas ini. Opsi Message Type dokodekan ‘8’.

## 1.2 Konfigurasi
### 1.2.1 Instalasi ISC-DHCP-Server
Kita akan menjadikan router **GEBANG** sebagai DHCP Server. Oleh karena itu, install **isc-dhcp-server** di **GEBANG** dengan melakukan langkah-langkah di bawah ini:

1. Update dulu **GEBANG**-nya dengan ```apt-get update```
2. Setelah mengupdate, install **isc-dhcp-server** dengan ```apt-get install isc-dhcp-server```

    **Keterangan** : Jangan panik dengan tulisan **[FAIL]**. Coba dibaca baik-baik, itu yang gagal bukanlah proses instalasinya, tetapi proses starting DHCP server-nya. Hal itu terjadi karena kita belum mengkonfigurasi interface-nya. Yuk capcus ke langkah selanjutnya!

### 1.2.2 Konfigurasi DHCP Server
Supaya DHCP Server bisa berjalan dengan baik, kita harus mengkonfigurasi interface-nya terlebih dahulu. Lakukanlah,

1. Buka ```/etc/default/isc-dhcp-server``` untuk mengkonfigurasi interface DHCP
    ```
    nano /etc/default/isc-dhcp-server
    ```
2. Tentukan interface DHCP-nya

    Coba cermati topologi kalian. Interface dari router **GEBANG** yang menuju ke client **NGAGEL**, **NGINDEN**, dan **DARMO** adalah **eth2**, maka kita akan memilih **eth2** sebagai interface DHCP.

    ```
    INTERFACES="eth2"
    ```
3. Buka ```/etc/dhcp/dhcpd.conf``` untuk mengatur range IP yang akan digunakan dalam DHCP server
    ```
    nano /etc/dhcp/dhcpd.conf
    ```
4. Tambahkan syntax berikut
    ```bash
    subnet 'NID' netmask 'Netmask' {
        range 'IP_Awal' 'IP_Akhir';
        option routers 'Gateway';
        option broadcast-address 'IP_Broadcast';
        option domain-name-servers 'DNS_yang_kalian_inginkan';
        default-lease-time 'Waktu';
        max-lease-time 'Waktu';
    }
    ```
    DHCP Server tidak hanya bisa mengatur range IP. Banyak parameter jaringan yang dapat diberikan oleh DHCP, seperti default gateway dan DNS server. Berikut ini beberapa **parameter jaringan** yang sering digunakan :

    [INI PENJELASAN]

    Sehingga, konfigurasinya menjadi seperti ini
    ```bash
    subnet 192.168.0.0 netmask 255.255.255.0 {
        range 192.168.0.10 192.168.0.20;
        option routers 192.168.0.1;
        option broadcast-address 192.168.0.255;
        option domain-name-servers 202.46.129.2;
        default-lease-time 600;
        max-lease-time 7200;
    }
    ```
5. Jangan lupa restart!
    ```bash
    service isc-dhcp-server restart
    ```
    Jika **failed**, maka stop dulu, kemudian start kembali
    ```bash
    service isc-dhcp-server stop
    service isc-dhcp-server start
    ```

### 1.2.3 Konfigurasi DHCP Client
Kita juga perlu mengkonfigurasi interface client supaya client tersebut mendapatkan IP dinamis dari DHCP server. Client yang akan kita berikan IP dinamis adalah **NGAGEL**, **NGINDEN**, dan **DARMO**. Lakukanlah,

1. Sebelumnya, coba cek terlebih dahulu IP **NGAGEL** dengan ```ifconfig```
    
    *[Gambar]*

    Dari konfigurasi sebelumnya, IP **NGAGEL** telah di set 192.168.0.2

2. Buka ```/etc/network/interfaces``` untuk mengkonfigurasi interface **NGAGEL**
    ```
    nano /etc/network/interfaces
    ```
3. Comment atau hapus konfigurasi yang lama, kemudian tambahkan syntax ini
    ```
    auto eth0
    iface eth0 inet dhcp
    ```
    **Keterangan**: **eth0** adalah interface yang digunakan client.

4. Jangan lupa restart!
    ```bash
    service networking restart
    ```
5. Testing

    Coba cek kembali IP **NGAGEL** dengan melakukan ```ifconfig```

    [Gambar]

    Yeay! IP **NGAGEL** telah berubah menjadi 192.168.0.10 sesuai dengan range IP yang diberikan oleh DHCP Server. Berarti DHCP Server kalian berhasil.

    **Keterangan** : 
    * Jika IP **NGAGEL** masih belum berubah, jangan panik. Lakukanlah kembali ```service networking restart```
    * Jika masih belum berubah juga, jangan buru-buru bertanya. Coba cek lagi semua konfigurasi yang telah kamu lakukan, mungkin ada typo-typo.

Lakukan kembali langkah-langkah di atas pada client **NGINDEN** dan **DARMO**.

## 1.2.4 Fixed Address

Ternyata PC **DARMO** selain menjadi client, juga akan digunakan sebagai server suatu aplikasi, sehingga akan menyulitkan jika IP nya berganti-ganti setiap connect WiFi. Oleh karena itu, **DARMO** membutuhkan IP tetap. Untungnya DHCP Server memiliki layanan untuk "menyewakan" alamat IP pada suatu host, yaitu menggunakan **Fixed Address**. Dalam kasus ini, **DARMO** akan mendapatkan IP tetap 192.168.0.25.

Lakukanlah,

1. Buka konfigurasi DHCP Server di router **GEBANG**
     ```bash
    nano /etc/dhcp/dhcpd.conf
    ```
    dan tambahkan syntax berikut
    ```bash
    host darmo {
        hardware ethernet 'hwaddress_darmo';
        fixed-address 192.168.0.25;
    }
    ```
    **Penjelasan:**
    
    * **hardware ethernet** didapatkan dari **hardware address** dari **DARMO**, dengan cara ```ifconfig```

        [gambar]
    * **fixed-address** adalah alamat IP yang "disewa" oleh **DARMO**
    
2. Jangan lupa restart!
    ```bash
    service isc-dhcp-server restart
    ```

3.  Buka konfigurasi interface **DARMO**
    ```bash
    nano /etc/network/interfaces
    ```
    Kemudian tambahkan **hardware address DARMO** pada konfigurasi
    ```bash
    hwaddress ether 'hwaddress_darmo'
    ```
    **Keterangan:** Hardware address perlu di-setting juga di **/etc/network/interfaces** karena perangkat yang kalian gunakan adalah perangkat virtual (UML) dimana hwaddress-nya akan berubah setiap kali di-restart

4. Jangan lupa restart!
    ```bash
    service networking restart
    ```

5. Testing

    Coba cek IP **DARMO** dengan melakukan ```ifconfig```

    [Gambar]

    Yeay! IP **DARMO** telah berubah menjadi 192.168.0.25 sesuai dengan Fixed Address yang diberikan oleh DHCP Server.

## 1.2.5 Final Testing

Setelah melakukan berbagai konfigurasi di atas, kalian bisa memastikan apakah DHCP Server kalian berhasil dengan cara

1. Matikan UML kalian
    ```bash
    bash bye.sh
    ```
2. Jalankan UML kembali 
    ```bash
    bash topologi.sh
    ```
3. Cek IP di semua client dengan ```ifconfig```

Jika **NGAGEL** dan **NGINDEN** berganti alamat IP dan **DARMO** tetap mendapatkan IP 192.168.0.25, maka DHCP server kalian berhasil dengan baik. 

## 1.3 Soal Latihan
1. 