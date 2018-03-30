# UML

## PENTING UNTUK DIBACA!!!
* Modul ini hanya berisi hal-hal yang berbeda dari sebelumnya saja. Selebihnya sama dengan [Modul Pengenalan UML](https://github.com/udinIMM/Modul-Pengenalan-UML)
* Jika kalian mendapatkan error, jangan buru-buru memanggil asisten. Silahkan dibaca dan dicermati kembali langkah-langkah pada [Modul Pengenalan UML](https://github.com/udinIMM/Modul-Pengenalan-UML) apakah sudah kamu jalankan semua?

## Membuat Topologi Jaringan

Topologi jaringan yang akan kalian gunakan adalah

![Topologi](https://github.com/mocatfrio/Jarkom-Modul-3/blob/master/UML/images/Capture.PNG)

1. Hapus terlebih dahulu file UML yang tidak diperlukan lagi bekas praktikum kemarin
    ```bash
    rm ALPHET MENYENG TJANGKIR CENGENGESAN WAYAHE switch1 switch2
    ```
2. Ubah file ```topologi.sh``` sesuai dengan gambar topologi di atas 

    ```bash
    #switch
    uml_switch -unix switch1 > /dev/null < /dev/null &
    uml_switch -unix switch2 > /dev/null < /dev/null &

    #router
    xterm -T GEBANG -e linux ubd0=GEBANG,jarkom umid=GEBANG eth0=tuntap,,,'ip_tuntap_tiap_kelompok' eth1=daemon,,,switch1 eth2=daemon,,,switch2 mem=256M &

    #proxy server
    xterm -T PUCANG -e linux ubd0=PUCANG,jarkom umid=PUCANG eth0=daemon,,,switch1 mem=128M &

    #client
    xterm -T NGAGEL -e linux ubd0=NGAGEL,jarkom umid=NGAGEL eth0=daemon,,,switch2 mem=96M &
    xterm -T NGINDEN -e linux ubd0=NGINDEN,jarkom umid=NGINDEN eth0=daemon,,,switch2 mem=96M &
    xterm -T DARMO -e linux ubd0=DARMO,jarkom umid=DARMO eth0=daemon,,,switch2 mem=96M &
    ```
**Keterangan** : 
* Jangan lupa mengubah **ip_tuntap_tiap_kelompok** sesuai kelompok masing-masing
* Memori router **GEBANG** ditambah karena akan menjadi DHCP Server
* Memori server **PUCANG** ditambah karena akan menjadi Proxy Server

## Konfigurasi IP

1. **GEBANG (Sebagai Router dan DHCP Server)**
    ```bash
    auto eth0
    iface eth0 inet static
    address 'ip_eth0_GEBANG_tiap_kelompok'
    netmask 255.255.255.252
    gateway 'ip_tuntap_tiap_kelompok'

    auto eth1
    iface eth1 inet static
    address 'ip_eth1_GEBANG_tiap_kelompok'
    netmask 255.255.255.248

    auto eth2
    iface eth2 inet static
    address 192.168.0.1
    netmask 255.255.255.0
    ```
2. **PUCANG (Sebagai Proxy Server)**
    ```bash
    auto eth0
    iface eth0 inet static
    address 'ip_PUCANG_tiap_kelompok'
    netmask 255.255.255.248
    gateway 'ip_eth1_GEBANG_tiap_kelompok'
    ```
3. **NGAGEL (Sebagai Client)**
    ```bash
    auto eth0
    iface eth0 inet static
    address 192.168.0.2
    netmask 255.255.255.0
    gateway 192.168.0.1
    ```
4. **NGINDEN (Sebagai Client)**
    ```bash
    auto eth0
    iface eth0 inet static
    address 192.168.0.3
    netmask 255.255.255.0
    gateway 192.168.0.1
    ```
5. **DARMO (Sebagai Client)**
    ```bash
    auto eth0
    iface eth0 inet static
    address 192.168.0.4
    netmask 255.255.255.0
    gateway 192.168.0.1
    ```

**Keterangan:**
* Ip_eth0_GEBANG_tiap_kelompok = NID_tuntap_tiap_kelompok + 2
* Ip_tuntap_tiap_kelompok = NID_tuntap_tiap_kelompok + 1
* Ip_eth1_GEBANG_tiap_kelompok = NID_DMZ_tiap_kelompok + 1
* Ip_PUCANG_tiap_kelompok = NID_DMZ_tiap_kelompok + 2
