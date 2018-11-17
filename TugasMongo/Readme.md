# Langkah-langkah Implementasi
1. Melakukan `vagrant init`
2. Selanjutnya mengubah Vagrantfile menjadi [Vagrantfile ini](./mongodb/Vagrantfile ). Vagrantfile ini membuat 4 VM, melakukan provisioning dan juga mengeset ip pada setiap VM.
3. Menambahkan beberapa file di folder asset
4. Jalankan vagrant up
5.  Pada setiap node mengubah setting mongodb pada file /etc/mongod.conf
    ```conf
    net:
      port: 27017
      bindIp: ipaddress
    replication:
      replSetName: mongodb-rs
    ```
    Lalu restart mongod `sudo service mongod restart`
6. Pada node manager melakukan `mongo --host ipaddress` lalu melakukan step
    ```mongo
    rs.initiate({_id:"mongodb-rs",members:[{_id:0,host:"ipaddress:27017"}]})
    rs.add("ipaddressnodelain")
    rs.status();
    ```
7. Melakukan importing ke mongodb manager node menggunakan `mongoimport --db laravel --collection jadwal --file file.json`
8. Mengecek pada node lain menggunakan `mongo --host ipaddressnode` pastikan terdapat tulisan SECONDARY. Lalu untuk melihat hasil replikasi dapat menggunakan cara
    ```
    rs.slaveOk()
    use laravel;
    db.jadwal.find();
    ```
9. Pada manager node membuat file laravel dengan cara 
    ```bash
    sudo apt-get install apache2
    sudo apt-get install software-properties-common
    sudo add-apt-repository ppa:ondrej/php
    sudo apt-get update
    sudo apt install php7.2 libapache2-mod-php7.2 php7.2-common php-7.2-cli php7.2-mongodb php-pear php7.2-dev
    sudo pecl install mongodb
    sudo bash
    sudo echo "extension=mongodb.so" >> /etc/php/7.2/apache2/php.ini
    sudo service apache2 restart

    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    php -r "unlink('composer-setup.php');"
    #install laravel
    cd /var/www
    sudo composer create-project --prefer-dist laravel/laravel mongo -vvv
    cd /var/www/mongo
    composer require jenssegers/mongodb
    ```
10. Untuk melakukan setting laravel support mongodb dapat mengikuti [link ini](https://appdividend.com/2018/05/10/laravel-mongodb-crud-tutorial-with-example/)