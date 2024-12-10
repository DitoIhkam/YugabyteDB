![alt text](?raw=true)

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

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/2.%20PYTHON%20VER%20YB%20NODE.png.png?raw=true)

Yugabyte Anywhere : 

https://docs.yugabyte.com/preview/yugabyte-platform/prepare/server-yba/
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/1.%20PYTHON%20VER%20YBA.png?raw=true)

Cek versi python dan python3 di almalinux dengan menggunakan command ini :
```
python3 -V
```
```
python -V
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/3.%20python%20-V%20python3%20-V.png?raw=true)



apabila belum terinstall versi yang sesuai, install dengan command dibawah
```
sudo yum install python38 -y
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/4.%20Install%20python%2038.png?raw=true)

Lalu, pastikan OS mengggunakan versi yang diinginkan dengan command ini
python
```
sudo alternatives --config python
```


python3
```
sudo alternatives --config python3
```

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/5.%20Config%20python%20dan%20python3.png?raw=true)

### Nano dan wget

Karna saya terbiasa menggunakan nano untuk editor dibanding vim, ini bersifat opsional.
Sedangkan untuk wget kita perlu menginstall di almalinux untuk mengunduh filenya
```
sudo yum install nano -y
```
```
sudo yum install wget -y
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/6.%20Install%20nano%20dan%20wget.png?raw=true)



### Ulimit Config

Di Linux, ulimit digunakan untuk membatasi dan mengontrol penggunaan sumber daya sistem (thread, file, dan koneksi jaringan) 

check limitnya menggunakan command ini
```
ulimit -a
```


kita perlu mengatur limitnya di `/etc/security/limits.conf`
konfigurasi ini biasa nya ada dengan OS berbasis RHEL. edit file limits.conf nya di akhir isi file dengan isi berikut, kemudian reboot OS nya dengan `sudo reboot`
```
sudo nano /etc/security/limits.conf
```

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
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/7.%20Edit%20ulimit.png?raw=true)

### SSE2 dan SSE4

YugabyteDB memerlukan SSE2 dan SSE4.2 untuk memastikan kompatibilitas dan kinerja optimal di sistem modern. Pastikan prosesor di server Anda mendukung kedua instruksi ini sebelum menginstal YugabyteDB. Cek apakah sudah terinstall atau belum menggunakan command dibawah

```
cat /proc/cpuinfo | grep sse2
```
```
cat /proc/cpuinfo | grep sse4.2
```

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/8.%20Check%20SSE.png?raw=true)


### Network/Port

Beberapa port dibawah ini harus diizinkan agar Service YugabyteDB bisa berjalan dengan baik. Dalam hal ini agar mempermudah karna development saja, matikan firewalld dengan 
```
sudo systemctl stop firewalld
```

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/9.%20Firewalld.png?raw=true)

| **Purpose**                          | **Port** | **Description**                                |
|--------------------------------------|----------|------------------------------------------------|
| YB-Master RPC communication          | 7100     | Communication between YB-Master nodes         |
| YB-TServer RPC communication         | 9100     | Communication between YB-TServer nodes        |
| YB-Master Admin UI                   | 7000     | Viewing the YB-Master dashboard               |
| YSQL (database access)               | 5433     | Accessing the database via YSQL               |
| YCQL (database access)               | 9042     | Accessing the database via YCQL               |

### Update package 

```
sudo yum update -y
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/10.%20Sudo%20yum%20update.png?raw=true)

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
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/11.%20Download%20and%20untar.png?raw=true)

Masuk ke direktori `yugabyte-2.23.1.0-b220-linux-x86_64/bin`


Jalankan post install untuk memastikan dependensi dan izin terpenuhi
```
sudo ./post_install.sh
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/12.%20Change%20Directory%20and%20post%20install.png?raw=true)

### Set Up Time synchronization

YugabyteDB mengandalkan sinkronisasi jam untuk menjamin konsistensi dalam transaksi terdistribusi. chrony adalah implementasi NTP yang lebih disukai untuk sinkronisasi jam. Setelah yugabytedb didownload dan di untar, kita baru bisa mengkonfigurasikan ini.

Install Chrony
```
sudo yum install -y chrony
```


masih didalam folder `yugabyte-2.23.1.0-b220-linux-x86_64/bin`, jalankan file bernama `configure_clockbound.sh` dengan command dibawah
```
sudo bash ./configure_clockbound.sh
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/13.%20Install%20Chrony%20and%20clockbound%20bash.png?raw=true)


setelah itu, periksa prosesnya apakah berjalan
```
systemctl status clockbound
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/14.%20Status%20Clockbound.png?raw=true)

Setelah itu terakhir install yugabyte di dalam direktori yang sama `yugabyte-2.23.1.0-b220-linux-x86_64/bin` dengan command berikut.
```
sudo ./yugabyted start
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/15.%20sudo%20yugabyted%20start.png?raw=true)

Berikut Hasil dari command `./yugabyted start` Seperti yugabyte UI, YB-Master, YB-TServer.

Yugabyte UI
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/16.%20Yugabyte%20UI%201.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/17.%20Yugabyte%20UI%202.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/18.%20Yugabyte%20UI%203.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/19.%20Yugabyte%20UI%204.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/20.%20Yugabyte%20UI%205.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/20.%20Yugabyte%20UI%206.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/21.%20Yugabyte%20UI%207.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/22.%20Yugabyte%20Master.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/23.%20Yugabyte%20Tserver.png?raw=true)

## Node Follower Deployment & Database sample

### Node Follower

Pada praktek sebelumnya, saya sudah mendeploy 1 node yugabyte dengan beberapa persiapan seperti python dan lain2 yang saya jelaskan sebelumnya. Untuk node 2 dan node 3 sama step nya seperti sebelumnya, namun perbedaannya hanya diakhir step `./yugabyted start`. Pada node 2 dan 3 sebagai follower, gunakan command berikut untuk menghubungkan ke node master yang sudah ada.
```
./yugabyted start --join=<ip-master>
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/24.%20Yugabyte%20Join%201.png?raw=true)
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/25.%20Yugabyte%20Join%202.png?raw=true)

Setelah follower terkoneksi dengan master yang sudah ada, berikut adalah tampilan dari yugabyte ui maupun yugabyte master (YB-Master) yang ada di master maupun di follower yang sudah terkoneksi 3 node.

Yugabyte UI dengan tampilan 3 node sudah terkoneksi
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/26.%20YugabyteUI%20with%203%20node%20connect.png?raw=true)
Yugabyte Master dengan 1 master leader dan 2 follower serta 3 tserver terkoneksi
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/27.%20YBMaster%20n%20Tserver%203%20node.png?raw=true)

## Database Demo

### Tarik database demo
Setelah yugabyte terinstall menggunakan `./yugabyted start`, pada dasarnya folder baru bernama var untuk yugabyte terbuat otomatis di `~/var` . Gunakan command berikut untuk membuat database demo secara otomatis di lokasi folder `var`
```
sudo ./yugabyted demo connect --base_dir=~/var
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/28.%20Create%20Database%20Demo%20and%20show%20database.png?raw=true)

### Tampilan di UI

Didalam yugabyte UI, data-data tidak bisa dilihat secara langsung, namun hanya terlihat ada database apa saja seperti tampilan berikut.

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/29.%20Database%20Demo%20YSQL%20in%20UI.png?raw=true)

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20YugabyteDB/image/30.%20Database%20Demo%20YSQL%20in%20UI%202.png?raw=true)



