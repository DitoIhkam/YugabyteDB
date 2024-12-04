# Yugabyte Deployment Dev

## Hardware requirements

* 4 cores
* 8 GB memory
* 215 GB disk space
* x86 (Intel, AMD) architecture


## Software requirements

* x86 Linux operating system
* License
* Python 3.8-3.11
* Sudo root permissions for installation

### Linux OS

Pada praktek kali ini, saya menggunakan almalinux 9.x
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.1%20OS.png?raw=true)

### License

ekstensi .lic yang didapat dari principal, pindahkan filenya ke /opt/yba-ctl setelah untar file yba dan melakukan preflight

### Python

Cek versi python dan python3 menggunakan command berikut :
```
sudo python -V
```
```
sudo python3 -V
```

Apabila python belum terinstall, gunakan command berikut untuk install

- Update  almalinux package terlebih dahulu
```
sudo dnf update -y -y
```
- Install Python version (support 3.8-3.11 version) pada versi ini saya menggunakan versi 3.9
```
sudo dnf install python39 -y
```
cek apakah versi python sudah ke 3.9 menggunakan command dibawah. Apabila belum, ubah ke versi 3.9
```
sudo alternatives --config python
```
```
sudo alternatives --config python3
```
> Choose the python support version

### Permission

..

## Download and extract

### Install wget apabila belum tersedia
```
sudo dnf install wget -y
```

### Download yugabyte
```
wget https://downloads.yugabyte.com/releases/2.23.1.0/yba_installer_full-2.23.1.0-b220-linux-x86_64.tar.gz
```

### Untar Yugabyte
```
tar xvzf yba_installer_full-2.23.1.0-b220-linux-x86_64.tar.gz
```

setelah itu, pergi ke direktori yba
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.2%20Download%2C%20untar%2C%20and%20cd.png?raw=true)
> Proses download, untar, dan cd ke yba

### Preflight check 

prefligth ini berfungsi untuk memeriksa kebutuhan sebelum menginstall yugabyte yang diperlukan.  nantinya akan dibuat konfigurasi otomatis di folder  /opt/yba-ctl
```
sudo ./yba-ctl preflight
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.3%20spreflight%20utf%20dan%20license%20perlu%20diperbaiki.png?raw=true)
Setelah menjalankan command ini, dia akan membuat folder /opt/yba-ctl dan beberapa konfigurasi didalamnya, serta membuat user yugabyte. Tetapi walaupun dia menawarkan untuk dibuatkan user `yugabyte` dan kita pilih yes, dia tetap tidak akan membuat user yugabyte. Artinya nanti kita perlu membuat user `yugabyte` sendiri

### Masalah validate-locale-config
Locale adalah pengaturan sistem yang menentukan format tampilan untuk tanggal, waktu, angka, dan karakter berdasarkan bahasa dan negara tertentu. Dalam hal ini, en_US.UTF-8 merujuk pada locale bahasa Inggris (United States) dengan encoding UTF-8.

Kebetulan di OS saya ini bermasalah, maka saya perlu menjalankan command dibawah ini untuk menginstallnya :
```
sudo dnf install glibc-langpack-en
```

lalu cek preflight lagi
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.4%20UTF%20sudah%20selesai%20.png?raw=true)

### Masalah user
Seperti yang sudah saya bilang sebelumnya, pada command `./yba-ctl preflight` untuk pengecekan pre-install, ini akan meminta untuk membuat user yugabyte dan apabila kita klik yes/y, user yugabyte tetap tidak terbuat. maka kita perlu membuat secara manual usernya.

buat user yugabyte 
```
adduser yugabyte
```
buat password jika memungkinkan
```
passwd yugabyte
```

### Activate lisensi

Pastikan lisensi baru sudah tersediadan sudah dipindahkan di /opt/yba-ctl
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.5%20lisensi%20sudah%20tersedia.png?raw=true)

Aktivasi lisensi
```
./yba-ctl license add -l /opt/yba-ctl/yba.lic 
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.6%20Lisensi%20sudah%20aktif.png?raw=true)

### Cek preflight Terakhir
Karna ini untuk kebutuhan development, maka untuk pengecekan preinstall seperti cpu, memory, dan disk masih bisa di lewati
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.7%20cek%20preflight.png?raw=true)

### Install YBA

pastikan berada di direktori yugabyte yang sudah di untar tadi, lalu jalankan command berikut dengan melewati beberapa preinstall minimum

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.8%20install%20yugabyte%20dengan%20skip%20bbrp%20karna%20dev%20aja.png?raw=true)

Berikut untuk hasil penginstallannya. 
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/0.9%20Hasilnya%2C%20jangan%20lupa%20cek%20port%20dan%20firewall%20yang%20menyala.png?raw=true)

Setelah install yugabyte, jangan lupa untuk mengecek apakah firewall aktif/tidak seperti firewalld/ufw. 
```
sudo systemctl status firewalld
```
apabila firewall aktif, ada dua opsi yang bisa dilakukan
1. Matikan service firewall (firewalld/ufw/lainnya)
2. Izinkan beberapa port untuk yugabyte

### Akses Yugabyte 
setelah ini, jalankan yugabyte ui di browser dengan https, seperti contoh dibawah
```
https://10.100.13.111
```
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/1.%20sDAFTAR%20AKUN.png?raw=true)

Daftarkan akunnya. Setelah akun didaftarkan. Login ke akun yugabyte yang sudah dibuat

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/2.%20LOGIN.png?raw=true)

Berikut adalah tampilan YugabyteUI
![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/3.%20YugabyteUI.png?raw=true)

## Masalah/kebingungan

1. Kebingungan ketika yugabyte menawarkan untuk dibuatkan user `yugabyte` pada saat preflight pertama kali menggunakan command `./yba-ctl preflight`. Ketika kita enter y/yes dan kita melakukan preflight ulang yang kedua kalinya menggunakan `./yba-ctl preflight`, yugabyte menawarkan ulang kedua kalinya untuk dibuatkan user yang padahal di preflight sudah Pass/tidak ada masalah. 
 
Pada akhirnya masalah ini terselesaikan dengan cara membuat user `yugabyte` secara manual, dan ketika melakukan preflight ulang, yugabyte sudah tidak menawarkan untuk membuat user `yugabyte`

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/KEBINGUNGAN.png?raw=true)

2. Masalah ketika melakukan konfigurasi permission, apakah semua command ini dimasukkan kedalam sudoers (sudo visudo)? dan untuk path nya apa dia mengarah ke dalam folder yang pertama kali di extract atau folder yang dibuat yugabyte setelah prefligth di /opt/yba-ctl ?

konfigurasi permission


![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/KEBINGUNGAN%202.png?raw=true)

Lokasi

![alt text](https://github.com/DitoIhkam/YugabyteDB/blob/main/Deployment%20Yugabyte/img/KEBINGUNGAN%20333.png?raw=true)
