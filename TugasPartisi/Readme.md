# Sakila DB
## Deskripsi Data Set
- Dataset ini terdiri dari 23 tabel
- Untuk melihat baris tiap-tiap tabel dapat dilihat dengan cara melakukan query `(select table_name,table_rows from information_schema.tables where table_schema='sakila')`
## Importing Data Set
- mysql -u root -p sakila < sakila-schema.sql untuk mengimport schema database sakila
- mysql -u root -p sakila < sakila-data.sql untuk mengimport data dari database sakila
## Partitoning Database Sakila Table Payment
1. Partitioning by Date Range
    ```sql
    CREATE TABLE payment (
    payment_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,customer_id SMALLINT UNSIGNED NOT NULL,staff_id TINYINT UNSIGNED NOT NULL,rental_id INT DEFAULT NULL,amount DECIMAL(5,2) NOT NULL,payment_date DATETIME NOT NULL,last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (payment_id,payment_date)
    -- KEY idx_fk_staff_id (staff_id),
    -- KEY idx_fk_customer_id (customer_id),
    -- CONSTRAINT fk_payment_rental FOREIGN KEY (rental_id) REFERENCES rental (rental_id) ON DELETE SET NULL ON UPDATE CASCADE,
    -- CONSTRAINT fk_payment_customer FOREIGN KEY (customer_id) REFERENCES customer (customer_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    -- CONSTRAINT fk_payment_staff FOREIGN KEY (staff_id) REFERENCES staff (staff_id) ON DELETE RESTRICT ON UPDATE CASCADE
    PARTITION BY RANGE(TO_DAYS(payment_date))(
        PARTITION from_2005_Januari_or_less VALUES LESS THAN(TO_DAYS('2005-01-31 23:59:59')),
        PARTITION from_2005_Februari VALUES LESS THAN(TO_DAYS('2005-02-28 23:59:59')),
        PARTITION from_2005_Maret VALUES LESS THAN(TO_DAYS('2005-03-31 23:59:59')),
        PARTITION from_2005_April VALUES LESS THAN(TO_DAYS('2005-04-30 23:59:59')),
        PARTITION from_2005_Mei VALUES LESS THAN(TO_DAYS('2005-05-31 23:59:59')),
        PARTITION from_2005_Juni VALUES LESS THAN(TO_DAYS('2005-06-30 23:59:59')),
        PARTITION from_2005_Juli VALUES LESS THAN(TO_DAYS('2005-07-31 23:59:59')),
        PARTITION from_2005_Agustus VALUES LESS THAN(TO_DAYS('2005-08-31 23:59:59')),
        PARTITION from_2005_September VALUES LESS THAN(TO_DAYS('2005-09-30 23:59:59')),
        PARTITION from_2005_Oktober VALUES LESS THAN(TO_DAYS('2005-10-31 23:59:59')),
        PARTITION from_2005_Nopember VALUES LESS THAN(TO_DAYS('2005-11-30 23:59:59')),
        PARTITION from_2005_Desember VALUES LESS THAN(TO_DAYS('2005-12-31 23:59:59')),
        PARTITION from_2006_Januari VALUES LESS THAN(TO_DAYS('2006-01-31 23:59:59')),
        PARTITION from_2006_Februari VALUES LESS THAN(TO_DAYS('2006-02-28 23:59:59')),
        PARTITION from_2006_Maret VALUES LESS THAN(TO_DAYS('2006-03-31 23:59:59')),
        PARTITION from_2006_April VALUES LESS THAN(TO_DAYS('2006-04-30 23:59:59')),
        PARTITION from_2006_Mei VALUES LESS THAN(TO_DAYS('2006-05-31 23:59:59')),
        PARTITION from_2006_Juni VALUES LESS THAN(TO_DAYS('2006-06-30 23:59:59')),
        PARTITION from_2006_Juli VALUES LESS THAN(TO_DAYS('2006-07-31 23:59:59')),
        PARTITION from_2006_Agustus VALUES LESS THAN(TO_DAYS('2006-08-31 23:59:59')),
        PARTITION from_2006_September VALUES LESS THAN(TO_DAYS('2006-09-30 23:59:59')),
        PARTITION from_2006_Oktober VALUES LESS THAN(TO_DAYS('2006-10-31 23:59:59')),
        PARTITION from_2006_Nopember VALUES LESS THAN(TO_DAYS('2006-11-30 23:59:59')),
        PARTITION from_2006_Desember VALUES LESS THAN(TO_DAYS('2006-12-31 23:59:59')),
        PARTITION from_up_Desember_2006 VALUES LESS THAN MAXVALUE
    );
    ```
    `MySQL table dengan InnoDB engine tidak mendukung partisi apabila tabelnya berisi foreign key, Solusinya comment foreign key dan tambahkan payment_date menjadi primary_key`
2. Partitioning by Hash
    ```sql
    CREATE TABLE payment (
        payment_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
        customer_id SMALLINT UNSIGNED NOT NULL,
        staff_id TINYINT UNSIGNED NOT NULL,
        rental_id INT DEFAULT NULL,
        amount DECIMAL(5,2) NOT NULL,
        payment_date DATETIME NOT NULL,
        last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        PRIMARY KEY (payment_id)
    )
    ENGINE=InnoDB DEFAULT CHARSET=utf8
    PARTITION BY HASH(payment_id)
    PARTITIONS 4;
    ```
3. Partitioning by Key
    ```sql
    CREATE TABLE payment (
        payment_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
        customer_id SMALLINT UNSIGNED NOT NULL,
        staff_id TINYINT UNSIGNED NOT NULL,
        rental_id INT DEFAULT NULL,
        amount DECIMAL(5,2) NOT NULL,
        payment_date DATETIME NOT NULL,
        last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        PRIMARY KEY (payment_id)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8
    PARTITION BY KEY()
    PARTITIONS 4;
    ```
## Partitioning Database Sakila Table Rental
1. Partitioning by Date Range
    ```sql
    CREATE TABLE rental (
        rental_id INT NOT NULL AUTO_INCREMENT,
        rental_date DATETIME NOT NULL,
        inventory_id MEDIUMINT UNSIGNED NOT NULL,
        customer_id SMALLINT UNSIGNED NOT NULL,
        return_date DATETIME DEFAULT NULL,
        staff_id TINYINT UNSIGNED NOT NULL,
        last_update TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        PRIMARY KEY (rental_id,rental_date)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8
    PARTITION BY RANGE (YEAR(rental_date))(
        PARTITION from_2004_or_less VALUES LESS THAN(2004),
        PARTITION from_2005 VALUES LESS THAN(2005),
        PARTITION from_2006 VALUES LESS THAN(2006),
        PARTITION from_2006_and_up VALUES LESS THAN MAXVALUE
    );
    ```
2. Partitioning by Hash
    ```sql
    CREATE TABLE rental (
        rental_id INT NOT NULL AUTO_INCREMENT,
        rental_date DATETIME NOT NULL,
        inventory_id MEDIUMINT UNSIGNED NOT NULL,
        customer_id SMALLINT UNSIGNED NOT NULL,
        return_date DATETIME DEFAULT NULL,
        staff_id TINYINT UNSIGNED NOT NULL,
        last_update TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE
        CURRENT_TIMESTAMP,
        PRIMARY KEY (rental_id)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8
    PARTITION BY HASH(rental_id)
    PARTITIONS 4;
    ```
3. Partitioning by Key
    ```sql
    CREATE TABLE rental (
        rental_id INT NOT NULL AUTO_INCREMENT,
        rental_date DATETIME NOT NULL,
        inventory_id MEDIUMINT UNSIGNED NOT NULL,
        customer_id SMALLINT UNSIGNED NOT NULL,
        return_date DATETIME DEFAULT NULL,
        staff_id TINYINT UNSIGNED NOT NULL,
        last_update TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE
        CURRENT_TIMESTAMP,
        PRIMARY KEY (rental_id)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8
    PARTITION BY KEY()
    PARTITIONS 4;
    ```
# Measures Data Set
## Deskripsi Dataset
- Deskripsi Singkat : Dataset berupa timeseries dengan 1846124 row
- Sumber data set : http://www.vertabelo.com/blog/technical-articles/everything-you-need-to-know-about-mysql-partitions
## Create Table
1. Tanpa Partitioning
    ```sql
    CREATE TABLE "measures" (
        "measure_timestamp" datetime NOT NULL,
        "station_name" varchar(255) DEFAULT NULL,
        "wind_mtsperhour" int(11) NOT NULL,
        "windgust_mtsperhour" int(11) NOT NULL,
        "windangle" int(3) NOT NULL,
        "rain_mm" decimal(5,2),
        "temperature_dht11" int(5),
        "humidity_dht11" int(5),
        "barometric_pressure" decimal(10,2) NOT NULL,
        "barometric_temperature" decimal(10,0) NOT NULL,
        "lux" decimal(7,2),
        "is_plugged" tinyint(1),
        "battery_level" int(3),
        KEY "measure_timestamp" ("measure_timestamp")
    )ENGINE=InnoDB DEFAULT CHARSET=latin1;
    ```
2. Menggunakan Partitioning
    ```sql
    CREATE TABLE `measures` (
        `measure_timestamp` datetime NOT NULL, `station_name` varchar(255) DEFAULT
        NULL, `wind_mtsperhour` int(11) NOT NULL,
        `windgust_mtsperhour` int(11) NOT NULL, `windangle` int(3) NOT NULL,
        `rain_mm` decimal(5,2), `temperature_dht11` int(5), `humidity_dht11` int(5),
        `barometric_pressure` decimal(10,2) NOT NULL,
        `barometric_temperature` decimal(10,0) NOT NULL, `lux` decimal(7,2), `is_plugged` tinyint(1),
        `battery_level` int(3), KEY `measure_timestamp` (`measure_timestamp`)
    ) ENGINE=InnoDB DEFAULT CHARSET=latin1
    PARTITION BY RANGE (YEAR(measure_timestamp)) (
    PARTITION to_delete_logs VALUES LESS THAN (2015),
    PARTITION prev_year_logs VALUES LESS THAN (2016),
    PARTITION current_logs VALUES LESS THAN (MAXVALUE)
    ) ;
    ```
## Import Data
- Download file zip
dari https://drive.google.com/file/d/0B2Ksz9hP3LtXRUppZHdhT1pBaWM/view
- Lakukan mysql –u root –p measures < sample_1_8_M_rows_data.sql


```
Untuk melakukan checking partition menggunakan
'explain select count * from table\G;'
```