# Tujuan 
Pada tugas ini, mahasiswa diharapkan mampu untuk
1. Membuat server basis data terdistribusi dengan
menggunakan konsep group replication

2. Mampu menambahkan load balancer (ProxySQL) untuk
membagi request ke server basis data

3. Menambahkan aplikasi CMS (Wordpress) yang
memanfaatkan arsitektur tersebut

4. Menguji kehandalan sistem (testing) dengan
menyimulasikan matinya beberapa node dan menunjukkan
bahwa data tetap tereplikasi pada node-node server basis
data.
# Proxy Server
- Sistem operasi Ubuntu 16.04
- MySQL Client:mysql-common_5.7.23,mysql-community-client_5.7.23,mysql-client_5.7.23
- Konfigurasi basis data:
proxysql.sql: mengubah user admin ProxySQL,
menambahkan user ‘monitoring’, menambahkan node
MySQL, menambahkan user ‘wordpress’
# Group Replication Server
- Sistem operasi Ubuntu 16.04
- MySQL Server: menggunakan community edition yang support group replication mysql-common_5.7.23,mysql-community-client_5.7.23,mysql-client_5.7.23,mysql-community-server_5.7.23
- Konfigurasi basis data:
    a. cluster_bootstrap.sql: melakukan bootstrapping MySQL
group replication
    b. cluster_member.sql: melakukan konfigurasi group replication pada node yang lain
    c. addition_to_sys.sql: patch script untuk ProxySQL
    d. create_proxysql_user.sql: create user untuk ProxySQL(‘monitor’ untuk monitoring, ‘wordpress’ untuk akses db wordpress)
# Langkah-langkah Implementasi
1. Melakukan `vagrant init`
2. Selanjutnya mengubah Vagrantfile menjadi [Vagrantfile ini](./mysql-cluster-proxysql/Vagrantfile). Vagrantfile ini membuat 4 VM, melakukan provisioning dan juga mengeset ip pada setiap VM.
3. Menambahkan beberapa file di folder asset
4. Jalankan vagrant up

# Group Replication
- Contoh akses node pada db1
- Mengakses database dengan ‘mysql -u root -p’
dengan password admin
- Membuat database wordpress dengan ‘create
database wordpress’
- Membuat user wordpress dengan cara
`CREATE USER 'wordpress'@'%' IDENTIFIED
BY 'wordpress';`
- Mengatur akses permission dengan cara
`GRANT ALL PRIVILEGES ON wordpress.* TO
'wordpress'@'%';’ dan ‘FLUSH PRIVILEGES;`
# PROXY SQL
-   Melakukan `mysql -u admin -p -h 127.0.0.1 -P
6032 < /vagrant/proxysql.sql` dengan password
admin untuk mengaktifkan proxy sql dan
mengeset group replication
-   Menambahkan user untuk group replication
baru dengan cara `mysql -u admin -p -h 127.0.0.1 -P 6032` dengan password "password"
-   `INSERT INTO mysql_users(username,
password, default_hostgroup) VALUES
('wordpress', 'wordpress', 2);`
-   `LOAD MYSQL USERS TO RUNTIME;`
-   `SAVE MYSQL USERS TO DISK;`
-   Menginstall wordpress
    ```bash
    sudo apt-get install apache2
    sudo apt-get install software-properties-common
    sudo add-apt-repository ppa:ondrej/php
    sudo apt-get update
    sudo apt install php7.1 libapache2-mod-php7.1 php7.1-common php7.1-mbstring php7.1-xmlrpc php7.1-soap php7.1-gd php7.1-xml php7.1-intl php7.1-mysql php7.1-cli php7.1-mcrypt php7.1-zip php7.1-curl
    cd /tmp && wget https://wordpress.org/latest.tar.gz
    tar -zxvf latest.tar.gz
    sudo mv wordpress /var/www/html/wordpress
    sudo mv wordpress /var/www/html/wordpress
    sudo chmod -R 777 /var/www/html/wordpress
    sudo nano /etc/apache2/sites-available/000-default.conf #lalu ubah pada bagian DocumentRoot menjadi ‘DocumentRoot /var/www/html/wordpress’
    sudo service apache2 restart
    ```