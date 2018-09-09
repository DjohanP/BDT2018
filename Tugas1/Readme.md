# Membuat 1 Master dan 4 SLave
## 1. Menginisialisasi server menggunakan [Vagrant](https://github.com/fathoniadi/cloud-2018/tree/master/vagrant)
### a. Menggunakan `vagrant init` untuk inisialisai
### b. Mengganti Vagrantfile menjadi config.vm.box = "ubuntu/xenial64"
### c. Melakukan uncoment pada `config.vm.network "private_network", ip: "192.168.31.10"` sesuaikan ip yang diinginkan
### d. vagrant up
### e. vagrant ssh
### f. sudo apt-get update pada vagrant
### g. sudo apt-get install mysql-server mysql-client
## 2. Mengeset Master Server Vagrant
### a. `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf `
### b. Edit `bind-address` menjadi ip static vagrant master
### c. Uncomment `server-id=1`
### d. Uncomment `log_bin=/var/log/mysql/mysql-bin.log`
### e. Uncomment `binlog_do_db            = databasename` jika ingin melakukan lebih dari 1 database bisa menambahkan `binlog_do_db=namadatabase`
### f. `sudo service mysql restart`
### g. Mengakses mysql menggunakan command `mysql -u root -p`
### h. Membuat User repl1 `create user 'repl1'@'%' identified by 'password';`
### i. Memberikan akses slave ke user repl1 menggunakan command `grant replication slave on *.* to 'repl1'@'%';`
### j. Membuat database `create database namadatabase` dan table `create table namatable(attribte)`
### k. `SHOW MASTER STATUS;` digunakan untuk mengecek posisi master
### l. Memberikan akses remote `GRANT ALL ON testing.* TO 'user'@'%' IDENTIFIED BY 'password';`
## 3. Setting pada slave
### a. `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf `
### b. Menggunakan langkah pada master dari langkah b. sampai e.
### c. Menambahkan baris `relay-log               = /var/log/mysql/mysql-relay-bin.log`
### d. `sudo service mysql restart`
### e. Mengakses mysql menggunakan command `mysql -u root -p`
### f. Menambahkan akses slave ke master menggunakan `CHANGE MASTER TO MASTER_HOST='192.168.31.10',MASTER_USER='repl1', MASTER_PASSWORD='password';`
### g. Menyalakan slave `START SLAVE;`
### h. Melihat status slave `SHOW SLAVE STATUS\G`
## 4. Promote slave to master
### a. `sudo serice mysql stop`
### b. Pada semua lakukan `STOP SLAVE;` dan juga `RESET SLAVE`
### c. Pada salah satu slave ganti server-id=1
### d. Semua slave diganti `CHANGE MASTER TO MASTER_HOST='ip slave baru',MASTER_USER='repl1', MASTER_PASSWORD='password';` dan lakukan `start slave`
## 4. Import database sakila pada master yang baru
### a. Pada setiap slave lakukan `STOP SLave;`
### b. Tambahkan `binlog_do_db=sakila` pada file mysqld.cnf di master yang baru
### c. `sudo service mysql restart`
### d. `create database sakila;`
### e. `mysql -u root -p sakila < sakila-mv-schema.sql`
### f. `mysql -u root -p sakila < sakila-mv-data.sql`
### g. Pada setiap slave lakukan `START SLAVE;`
## 5. Master yang lama menjadi slave
### a. Ubah `server-id=2` pada mysqld.cnf
### b. `sudo service mysql start`
### c. Lalu pada mysql lakukan `CHANGE MASTER TO MASTER_HOST='ip slave baru',MASTER_USER='repl1', MASTER_PASSWORD='password';` dan lakukan `start slave`