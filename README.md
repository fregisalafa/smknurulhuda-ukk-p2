# Konfigurasi Databases-VM and Webserver-VM UKK-P2

## config change hostname ubuntu
```bash
sudo hostnamectl set-hostname web-xx
```
## Memperbarui File hostname
```bash
sudo nano /etc/hosts
```
## cek direktori netplan
```bash
ls /etc/netplan/
```

## Perubahan IP Static Pada Server
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
```yaml
network:
  version: 2
  ethernets:
    ens18: # pastikan telah sesuai interfaces pada ip a
      dhcp4: no
      addresses:
        - 192.168.xx.3/29
      routes:
        - to: default
          via: 192.168.xx.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
## terapkan Konfigurasi IP
```bash
sudo netplan apply
```
## Masuk ke sebagai root

```bash
sudo su
```
# Konfigurasi Pada Databases-VM
## Install mysql server
```bash
apt install mysql-server -y
```
## Izinkan akses agar bisa dihubungkan ke Web Server
```bash
nano /etc/mysql/mysql.conf.d/mysqld.cnf
# Cari baris 'bind-address = 127.0.0.1', ubah menjadi '0.0.0.0'
```

## Restart MySQL
```bash
systemctl restart mysql
```
## Masuk ke MYSQL dan buat create database
```bash
mysql
```
Di dalam prompt MySQL (simbol mysql>), ketik:
```mysql
CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
# Untuk membuat database
CREATE USER 'fregi'@'%' IDENTIFIED BY 'salafa';
# Membuat user
GRANT ALL PRIVILEGES ON moodle.* TO 'fregi'@'%';
# memberikan akses penuh pada user yang dibuat
FLUSH PRIVILEGES;
# Restart database
EXIT;
# keluar dari mysql
```
# Konfigurasi Pada WebServer-VM
## Install aplikasi Apache
```bash
# Update daftar aplikasi
apt update

# Instal Apache dan semua kebutuhan Moodle
apt install apache2 php libapache2-mod-php php-cli php-mysql php-gd php-xml php-curl php-zip php-intl php-mbstring php-soap php-bcmath -y
```
## Masuk ke direktori apache
```bash
cd /var/www/html/
```
## Download moodle
```bash
wget https://download.moodle.org/download.php/direct/stable405/moodle-latest-405.tgz
```
## Extark moodle
```bash
tar -zxvf moodle-latest-405.tgz
```
## Buat direktori data
```bash
mkdir -p /var/www/moodledata
```
## Beri hak akses ke folder data
chown -R www-data:www-data /var/www/moodledata
chmod -R 777 /var/www/moodledata

## Beri hak akses ke folder program moodle
```bash
cd /var/www/html
chown -R www-data:www-data moodle
chmod -R 777 moodle
```

## Ubah konfigurasi pada PHP.ini
```bash
nano /var/www/html/etc/php/8.3/apache2/php.ini
# ubah setiap value serti di bawah
max_input_vars = 5000
memory_limit = 512M
post_max_size = 10M
upload_max_filesize = 10M
date.timezone = Asia/Jakarta
```

## Restart Apache
```bash
systemctl restart apache2
```

