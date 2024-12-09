# YugabyteDB Deployment

## Prerequisite

### OS

Pada praktek kali ini, saya menggunakan almalinux 8.x (Keluarga RHEL), tetapi untuk versi OS support lainnya bisa dilihat dibawah ini.


![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.1%20OS.png?raw=true)

### Hardware

Berikut informasi tentang minimum requirement yugabyte

| **Requirement Type** | **Cores**         | **RAM**           | **Description**                                  |
|-----------------------|-------------------|-------------------|-------------------------------------------------|
| Minimum               | 2 cores          | 2GB RAM           | Basic requirement for running YugabyteDB       |
| Production (YCQL)     | 16+ cores        | 32GB+ RAM         | Recommended for production environments (YCQL) |
| Production (YSQL)     | 16+ cores        | 64GB+ RAM         | Recommended for production environments (YSQL) |
| **Performance Tip**   | More CPU > More RAM | N/A              | Adding more CPU improves performance better    |


### Python

Pada YugabyteDB, versi python yang dibutuhkan antara 3.6-3.8. Sedangkan, untuk Yugabyte Anywhere versi yang dibutuhkan antara 3.8-3.11. Maka kita ambil yang support keduanya yaitu versi 3.8 (per 9 Desember 2024). Referensinya bisa dilihat di dokumentasi dan gambar dibawah

Yugabyte DB :

https://docs.yugabyte.com/preview/yugabyte-platform/prepare/server-nodes-software/software-on-prem-manual/

(gambar python db versi)

Yugabyte Anywhere : 

https://docs.yugabyte.com/preview/yugabyte-platform/prepare/server-yba/
(gambar python dba versi)

Cek versi python dan python3 di almalinux dengan menggunakan command ini :
```
python3 -V
```
```
python -V
```

apabila belum terinstall versi yang sesuai, install dengan command dibawah
```
sudo yum install python38
```

Lalu, pastikan OS mengggunakan versi yang diinginkan dengan command ini
python
```
sudo alternatives --config python
```
(gambar)

python3
```
sudo alternatives --config python3
```
(gambar)

### Nano dan wget

Karna saya terbiasa menggunakan nano untuk editor dibanding vim, ini bersifat opsional.
Sedangkan untuk wget kita perlu menginstall di almalinux untuk mengunduh filenya
```
sudo yum install nano
```
```
sudo yum install wget
```
(gambar)



### Ulimit Config

Di Linux, ulimit digunakan untuk membatasi dan mengontrol penggunaan sumber daya sistem (thread, file, dan koneksi jaringan) 

check limitnya menggunakan command ini
```
ulimit -a
```
(gambar)

kita perlu mengatur limitnya di `/etc/security/limits.conf`
konfigurasi ini biasa nya ada dengan OS berbasis RHEL. edit file limits.conf nya di akhir isi file dengan isi berikut, kemudian reboot OS nya
```
*                -       core            unlimited
*                -       data            unlimited
*                -       fsize           unlimited
*                -       sigpending      119934
*                -       memlock         64
*                -       rss             unlimited
*                -       nofile          1048576
*                -       msgqueue        819200
*                -       stack           8192
*                -       cpu             unlimited
*                -       nproc           12000
*                -       locks           unlimited
```


### SSE2 dan SSE4

YugabyteDB memerlukan SSE2 dan SSE4.2 untuk memastikan kompatibilitas dan kinerja optimal di sistem modern. Pastikan prosesor di server Anda mendukung kedua instruksi ini sebelum menginstal YugabyteDB. Cek apakah sudah terinstall atau belum menggunakan command dibawah

```
cat /proc/cpuinfo | grep sse2
```
```
cat /proc/cpuinfo | grep sse4.2
```


### Network/Port

Beberapa port dibawah ini harus diizinkan agar Service YugabyteDB bisa berjalan dengan baik. Dalam hal ini agar mempermudah karna development saja, matikan firewalld dengan `sudo systemctl stop firewalld`

| **Purpose**                          | **Port** | **Description**                                |
|--------------------------------------|----------|------------------------------------------------|
| YB-Master RPC communication          | 7100     | Communication between YB-Master nodes         |
| YB-TServer RPC communication         | 9100     | Communication between YB-TServer nodes        |
| YB-Master Admin UI                   | 7000     | Viewing the YB-Master dashboard               |
| YSQL (database access)               | 5433     | Accessing the database via YSQL               |
| YCQL (database access)               | 9042     | Accessing the database via YCQL               |

### Update package 

```
sudo yum update
```

## Download and Install YugabyteDB

### Download yugabyte

File yugabyteDB yang bisa didownload bisa dilihat di antara 2 link ini, namun saya memilih yang pertama 
1. 
```
https://docs.yugabyte.com/preview/quick-start/linux/
```
2. 
```
https://docs.yugabyte.com/preview/deploy/manual-deployment/install-software/
```

Download file nya
```
wget https://downloads.yugabyte.com/releases/2.23.1.0/yugabyte-2.23.1.0-b220-linux-x86_64.tar.gz
```

Untar Filenya
```
tar -xvzf yugabyte-2.23.1.0-b220-linux-x86_64.tar.gz
```

Masuk ke direktori `yugabyte-2.23.1.0-b220-linux-x86_64`, lalu `bin`


konfigurasi post install untuk memastikan dependensi dan izin terpenuhi
```
./post_install.sh
```

### Set Up Time synchronization

YugabyteDB mengandalkan sinkronisasi jam untuk menjamin konsistensi dalam transaksi terdistribusi. chrony adalah implementasi NTP yang lebih disukai untuk sinkronisasi jam

Install Chrony
```
sudo yum install -y chrony
```

masih didalam folder `yugabyte-2.23.1.0-b220-linux-x86_64/bin`, jalankan file bernama `configure_clockbound.sh` dengan command dibawah
```
sudo bash ./configure_clockbound.sh
```

setelah itu, periksa prosesnya apakah berjalan
```
systemctl status clockbound
```

Setelah itu terakhir install yugabyte di dalam direktori yang sama `yugabyte-2.23.1.0-b220-linux-x86_64/bin` dengan command berikut.
```
./yugabyted start
```

command diatas dijalankan hanya di master leader, untuk followernya di 2 vm lain (dalam hal ini total vm ada 3) gunakan command dibawah agar vm nya menjadi follower
```
./yugabyted start --join=<ip-master>
```

1. sudo dnf update / sudo yum update / sudo apt update
2. python -V / python --version / python3 -V / python3 --version
3. Download python 3.9 or newer (dont do it if already)
4. sudo alternatives --config python (for almalinux yang gabisa dicek python -V nya)
5. sudo yum install nano (kalo mau pake nano)
6. ulimit config
7. download using wget (install wget dulu)
9. untar
10. post install
11. cd /yugabyte/bin, then ./yugabyted start 


sudo dnf update
