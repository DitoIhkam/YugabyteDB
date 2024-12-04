## Hardware requirements
The server for YugabyteDB Anywhere must meet the following hardware requirements:

4 cores
8 GB memory
215 GB disk space
x86 (Intel, AMD) architecture


## Software requirements
YugabyteDB Anywhere has the following software requirements:

x86 Linux operating system
License
Python 3.8-3.11
Sudo root permissions for installation

### Linux OS

(gambar)

### License

.lic extension format from principal

### Python

Check python and python3 version using
```
sudo python -V
```
```
sudo python3 -V
```

if not installed, install using this command

- Update First the almalinux
```
sudo dnf update -y -y
```
- Install Python version (support 3.8-3.11 version)
```
sudo dnf install python39 -y
```
change python version or make sure
```
sudo alternatives --config python
```
```
sudo alternatives --config python3
```
> Choose the python support version

### Permission

Skip, just for production


## Download and extract

### Install wget if not available
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

### Go to directory and check preflight

### Preflight check yang nanti bakal buat konfigurasi otomatis ke /opt/yba-ctl
```
sudo ./yba-ctl preflight
```
dia akan membuat folder /opt/yba-ctl dan membuat user yugabyte. Tetapi user yugabyte tetap tidak akan dibuat dan harus dibuat manual. 

### Masalah UTF8
install berikut 
```
sudo dnf install glibc-langpack-en
```
(gambar)
lalu cek preflight lagi

buat user yugabyte beserta password
```
adduser yugabyte
```
buat password jika memungkinkan
```
passwd yugabyte
```

### Activate lisensi

Pastikan lisensi baru sudah tersedia di /opt/yba-ctl
(gambar)

Aktivasi lisensi
```
./yba-ctl license add -l /opt/yba-ctl/yba.lic 
```
(pastikan license sudah pindah kesini)
(gambar)

./yba-ctl start (untuk start semua)
./yba-ctl stop (untuk stop semua)


Upgrading
- Download the installer (wget https://downloads.yugabyte.com/releases/<version>/yba_installer_full-version.tar.gz.
- untar the tar.gz (tar -xvzf yba_installer_full-version.tar.gz)
- cd yba_installer_full-version/

- sudo ./yba-ctl upgrade
