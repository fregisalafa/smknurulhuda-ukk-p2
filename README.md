# UKK-P2 SMK Nurul Huda Paguyangan
## Konfigurasi VM web-xx
Masuk ke user root
``` bash
sudo su
```
Setting ip untuk web-xx
```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: no
      addresses:
        - 192.168.xx.2/29
      routes:
        - to: default
          via: 192.168.xx.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
Beri izin hanya pada user root
```bash
sudo chmod 600 /etc/netplan/00-installer-config.yaml
```
Terapkan koneksi netplan
```bash
sudo netplan apply
```
Cek koneksi internet
```bash
ping 8.8.8.8
```

## Konfigurasi VM db-xx
Masuk ke user root
``` bash
sudo su
```
Ubah nama hostname
```bash
hostnamectl set-hostname db-xx
```
```bash
nano /etc/hosts

#ubah web mejadi db
```
Ubah alamat ip  pada vm-db
```bash
sudo nano /etc/netplan/00-installer-config.yaml

# ubah ip 192.168.xx.2 menjadi 192.168.xx.3
```
Terapkan koneksi netplan
```bash
sudo netplan apply
```
Cek koneksi internet
```bash
ping 8.8.8.8
```

# Kofigurasi server via CMD
## konfiurasi pada vm db via CMD
Akses server db via CMD
```cmd
ssh ukkxx@192.168.xx.2
```
Switch ke user root
```bash
sudo su
```
Perbarui repositori
```bash
apt update
```
Install mysql-server
```bash
apt install mysql-server -y
```
Beri akses agar databases bisa diakses di luar local
```bash
nano /etc/mysql/mysql.conf.d/mysqld.cnf
# Cari baris 'bind-address = 127.0.0.1', ubah menjadi '0.0.0.0'
```
Masuk ke MYSQL
```bash
mysql
```
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
Restart MYSQL
```bash
systemctl restart mysql
```
## konfiurasi pada vm web via CMD
Akses server db via CMD
```cmd
ssh ukkxx@192.168.xx.3
```
Switch ke user root
```bash
sudo su
```
Perbarui repositori
```bash
apt update
```
Instal Apache dan semua kebutuhan Moodle
```bash
apt install apache2 php libapache2-mod-php php-cli php-mysql php-gd php-xml php-curl php-zip php-intl php-mbstring php-soap php-bcmath -y
```
Masuk ke direktori apache
```bash
cd /var/www/html/
```
Download moodle
```bash
wget https://download.moodle.org/download.php/direct/stable405/moodle-latest-405.tgz
```
Extark moodle
```bash
tar -zxvf moodle-latest-405.tgz
```
Buat direktori moodledata
```bash
mkdir -p /var/www/moodledata
```
Beri hak akses ke folder data
```bash
chown -R www-data:www-data /var/www/moodledata
chmod -R 777 /var/www/moodledata
```
Beri hak akses ke folder program moodle
```bash
chown -R www-data:www-data moodle
chmod -R 777 moodle
```
Ubah konfigurasi pada PHP.ini
```bash
nano /etc/php/8.3/apache2/php.ini
# ubah setiap value serti di bawah
max_input_vars = 5000
memory_limit = 512M
post_max_size = 10M
upload_max_filesize = 10M
```
Restart Apache
```bash
systemctl restart apache2
```
Ubah menjadi dinamis agar bisa diakses dari IP mana saja
```bash
nano /var/www/html/moodle/config.php
```
```php
$CFG->wwwroot = 'http://' . $_SERVER['HTTP_HOST'] . '/moodle';
```
Restart Apache
```bash
systemctl restart apache2
```

