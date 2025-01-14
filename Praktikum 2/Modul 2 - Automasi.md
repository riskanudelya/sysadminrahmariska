# Modul 2 - Automasi

## Dasar Teori

### Ansible

Ansible adalah sebuah provisioning tool yang dikembangkan oleh  RedHat. Dimana kamu dapat mencatat setiap proses deployment ataupun  konfigurasi yang biasa dilakukan berulang - ulang terhadap beberapa  server. Misal saat pertama kali kita memasang Ubuntu Server di 10 mesin, maka kita akan melakuan apt-get update serta memasang beberapa komponen seperti PHP5 dan Apache2. Sebenarnya tidak akan menjadi masalah, bila  kita hanya melakukan sedikit hal. Tapi bayangkan bila harus melakukan  konfigurasi yang cukup kompleks dan dilakukan secara berulang - ulang ke 10 mesin tersebut.

## Pra - Instalasi
Proses Set up autostart lxc yang belum tersetting
- debian_php5.6
  ![p1](asset2/p1.png)

- Tambahkan auto start = 1
  ![p2](asset2/p2.png)

- Berhasil tersetting autostart
  ![p3](asset2/p3.png)

- Setup semua lxc menjadi autostart ketika vm dinyalakan

  ![pi1](asset2/pi1.png)

- Masuk ke LXC

  ```bash
  sudo lxc-attach -n debian_php5.6
  ```
  ![pi21](asset2/pi21.png)
  ![pi22](asset2/pi22.png)

- Install openssh-server & python

  ```bash
  sudo apt install openssh-server python
  ```
  ![pi3](asset2/pi3.png)
  
- Config ssh untuk enable root user

  ```bash
  cd /etc/ssh
  nano sshd_config
  
  ![pi41](asset2/pi41.png)
  
  # setting config menjadi
  PermitRootLogin yes
  RSAAuthentication yes
  ```
  ![pi42](asset2/pi42.png)

- Restart ssh service

  ```bash
  sudo service sshd restart
  ```
- Setting password root

  ```bash
  passwd
  ```
  ps>100
  
  ![pi56](asset2/pi56.png)
  
- Akses LXC melalui SSH

  ```bash
  # jika masih di dalam lxc, silahkan di exit dulu
  
  ssh root@lxc_php5.dev
  ```
  ![pi7](asset2/pi7.png)

- Keluar dari ssh

  ```bash
  # bisa dengan menekan ctrl + D
  # atau menulis command
  exit
  ```
  ![pi8](asset2/pi8.png)
  
- Lakukan configurasi ini pada semua lxc

  **Konfigurasi juga di landing dan Ubuntu_php7.4**
  
- ubuntu_landing

  ![pi91](asset2/pi91.png)
  ![pi92](asset2/pi92.png)
  ![pi93](asset2/pi93.png)
  ![pi94](asset2/pi94.png)
  ![pi95](asset2/pi95.png)
  ![pi96](asset2/pi96.png)
  ![pi97](asset2/pi97.png)


- ubuntu_php7.4

  ![pi98](asset2/pi98.png)
  ![pi99](asset2/pi99.png)
  ![pi910](asset2/pi910.png)
  ![pi911](asset2/pi911.png)
  ![pi912](asset2/pi912.png)
  ![pi913](asset2/pi913.png)


## Instalasi

- Pada VM ubuntu server 20.04, install ansible

  ```bash
  sudo apt install ansible sshpass
  ```
  
  ![in1](asset2/in1.png)

## Getting started with Infrastructur as Code

### Cek Koneksi Semua LXC menggunakan Ansible

1. Buat folder simple-playbook pada ~/ansible/

   ```bash
   mkdir -p ~/ansible/simple-playbook
   ```
   ![c1](asset2/c1.png)
   

2. Pindah ke folder simple-playbook

   ```bash
   cd ~/ansible/simple-playbook
   ```
   ![c2](asset2/c2.png)

3. Buat file `hosts` sebagai vagrant inventory, dengan isi:
   
   ![c31](asset2/c31.png)

   ```bash
   ubuntu_landing ansible_host=lxc_landing.dev ansible_ssh_user=root ansible_become_pass=a
   debian_php5 ansible_host=lxc_php5.dev ansible_ssh_user=root ansible_become_pass=a
   ubuntu_php7 ansible_host=lxc_php7.dev ansible_ssh_user=root ansible_become_pass=a
   ```
   

   Keterangan syntax:

   ```bash
   nama_host ansible_host=[IP/DOMAIN] ansible_ssh_user=[UserName] ansible_become_pass=[password user]
   ```
   
   ![c32](asset2/c32.png)

4. Berikutnya jalankan perintah:

   ```bash
   ansible -i hosts -m ping all -k
   ```


   Keterangan:

   1. Parameter -i digunakan untuk digunakan untuk mendeclare ansible inventory.
   2. Parameter -m digunakan untuk declare module command
   3. Parameter all digunakan untuk penanda ansible dijalankan di host mana. Parameter all bisa diganti dengan nama host.
   4. Parameter -k digunakan untuk menanyakan password login ssh

   ![c41](asset2/c41.png)
   ![c42](asset2/c42.png)

### Menjalankan shell command menggunakan ansible

1. Jalankan perintah

   ```bash
   ansible -i ./hosts -m shell -a 'uname -a' all -k
   ```
   
   ![s1](asset2/s1.png)

## Grouping Host

1. Buka file `hosts` dan tambahkan nama group. Contoh nama groupnya adalah php5 dan php7

   ![g1](asset2/g1.png)

   ```bash
   [php5]
   ubuntu_landing ansible_host=lxc_landing.dev ansible_ssh_user=root ansible_become_pass=a
   debian_php5 ansible_host=lxc_php5.dev ansible_ssh_user=root ansible_become_pass=a
   
   [php7]
   ubuntu_php7 ansible_host=lxc_php7.dev ansible_ssh_user=root ansible_become_pass=a
   ```
   
   ![g12](asset2/g12.png)
   
2. Buat file `install-lynx.yml` dengan isi sebagai berikut
   
   ![g2](asset2/g2.png)
   
   ```yaml
   - hosts: php5
     tasks:
       - name: Install lynx
         become: yes #untuk menjadi superuser
         apt: name={{ item }} state=latest update_cache=true
         with_items:
           - lynx
   ```

   ![g22](asset2/g22.png)
    
   
3. Jalankan Perintah

   ```bash
    ansible-playbook -i hosts install-lynx.yml -k
   ```
   ![g3](asset2/g3.png)
   
4. Hasil

   ![g4](asset2/g4.png)
   
   hasil memastikan kembali 
   
   ![g42](asset2/g42.png)

5. Cek ke lxc group php5, apakah sudah terinstall

   ![g5](asset2/g5.png)

   ![g52](asset2/g52.png)

### Copy File

1. Buat file copy.yml, dengan isi
   
   ![cp1](asset2/cp1.png)

   ```yaml
   - hosts: php5
     tasks:
       - name: Buat folder /tmp/dari-host 
         command: mkdir -p /tmp/dari-host
       - name: Copy file install-lynx.yml
         copy: src=./install-lynx.yml dest=/tmp/dari-host/install-lynx.yml
   ```
   ![cp12](asset2/cp12.png)
   
2. Jalankan dengan perintah

   ```bash
    ansible-playbook -i hosts copy.yml -k
   ```

   ![cp2](asset2/cp2.png)
   
   ![cp22](asset2/cp22.png)

3. Cek ke lxc group php5, apakah sudah terinstall

   ![cp3](asset2/cp3.png)

   ![cp32](asset2/cp32.png)
   
   
   
*Exit dan masuk kembali ke server Ubuntu kita*


![cp4](asset2/cp4.png)
   

## Referensi

1. https://github.com/leucos/ansible-tuto
2. https://github.com/fathoniadi/cloud-2018/tree/master/ansible
3. https://www.youtube.com/watch?v=f6cKT0aylDo

## Warning !!!

Untuk mengantisipasi hal yang tidak diinginkan (Ingat LXC digunakan sampai modul 5) silahkan backup semua lxc menggunakan command:

```bash
lxc-stop -n ubuntu_landing
lxc-copy -n ubuntu_landing -N ubuntu_landing_backup -sKD

# jangan lupa untuk mematikan auto start di config lxc backup
```
1.	Cek terlebih dahulu lxc kita
    ![w1](asset2/w1.png)

2.	Stop Ubuntu landing dan lxc lainnya sebelum melakukan backup
 	  ![w2](asset2/w2.png)

3.	Kemudian backup lxc kali ini menggunakan –sKD
    ![w3](asset2/w3.png)
    

4.	jangan lupa untuk mematikan auto start di config lxc backup

    - Debian
      ![w4](asset2/w4.png)
      ![w42](asset2/w42.png)
    
    - Landing
      ![w43](asset2/w43.png)
      ![w44](asset2/w44.png)
      
    - Ubuntu_php7.4
      ![w45](asset2/w45.png)
      ![w46](asset2/w46.png)
    
5.	Exit dan cek lxc list
    ![w5](asset2/w5.png)
   
    Masuk kembali ke server
    ![w52](asset2/w52.png)

    Cek lxc list untuk memastikan autostart pada lxc backup telah nonaktifkan 
    ![w53](asset2/w53.png)

6.	#steps boleh dilewati
    Untuk cek autostart berjalan maka reboot server Ubuntu
    Masuk kembali ke server 
    ![w6](asset2/w6.png)
    
    Cek lxc list
    ![w62](asset2/w62.png)
    
    #sudah sama dengan modul
    ![w63](asset2/w63.png)

## Soal Latihan Praktikum (*DIWYOR*)

**Do It With Your Own Risk xixixi.**

1. Buat SQL Server dengan lxc baru bernama lxc_mariadb, gunakan ansible untuk instalasi dan konfigurasi mariadb serta phpmyadmin.

   - Setup LXC lxc_mariadb

     ```bash
     sudo lxc-create -n lxc_mariadb -t download -- --dist ubuntu --release bionic --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
     
     sudo lxc-start -n lxc_mariadb
     ```
    ![t1](asset2/t1.png)
    
    Maka lakukan lagi dengan memberi perintah sudo dan cek list lxc kembali
    ![t12](asset2/t12.png)
    
    Start lxc mariadb
    ![t13](asset2/t13.png)
    
    
   - Set IP static lxc_mariadb 10.0.3.200

     ```bash
     sudo lxc-attach -n lxc_mariadb
     apt update; apt upgrade -y; apt install -y nano
     ```

     ![t14](asset2/t14.png)
     
     Tunggu proses
     ![t15](asset2/t15.png)
     
     Cek ulang
     ![t16](asset2/t16.png)

     `nano /etc/netplan/10-lxc.yml`
     ![t17](asset2/t17.png)
     
     tampilan sebelumnya...
     ![t18](asset2/t18.png)
     
     tampilan sesudah diedit...
     ![t19](asset2/t19.png)
     
     ```bash
     netplan apply
     ```
     
     ![t110](asset2/t110.png)
     
     
   - Konfigurasi ssh server untuk lxc_mariadb

     ```bash
     apt install openssh-server
     nano /etc/ssh/sshd_config
     
     # setting config menjadi
     PermitRootLogin yes
     RSAAuthentication yes
     
     # end of config
     service sshd restart
     ```

    ![t111](asset2/t111.png)
    
    Tunggu proses
    ![t112](asset2/t112.png)

    ![t113](asset2/t113.png)
    
    ![t114](asset2/t114.png) 
    
    ![t115](asset2/t115.png)

   - Set password lxc_mariadb

     ```bash
     passwd
     ```
     
     ps100
     
     ![t116](asset2/t116.png)
     
     
   - Setting autostart lxc_mariadb

     ```bash
     exit
     sudo su
     cd /var/lib/lxc/lxc_mariadb
     nano config
     ```
     
     Exit dan save yes
     ![t117](asset2/t117.png)

     ![t118](asset2/t118.png)
     


   - Jangan lupa dibackup dulu :)
   
     *Sebelum backup saya akan reboot server Ubuntu untuk cek apakah autostart mariadb berubah, setelah itu saya akan stop running dan back up*

     ![t119](asset2/t119.png)

    *Berhasil*
     ![t120](asset2/t120.png)

    *Stop running*
     ![t121](asset2/t121.png)

    *Matikan autostart mariadb_backup*
     ![t122](asset2/t122.png)
     ![t123](asset2/t123.png)

     ![t124](asset2/t124.png)
     ![t125](asset2/t125.png)
     
     
     
   - Daftar kan domain lxc_mariadb ke /etc/hosts

     ```bash
     sudo nano /etc/hosts
     ```

     ![t126](asset2/t126.png)
     ![t127](asset2/t127.png)
     

   - Buat ansible untuk install dan  konfigurasi mariadb 

     - Buat folder baru untuk mengerjakan ansible soal -latihan

       ```bash
       cd ~/ansible
       mkdir modul2-ansible
       cd modul2-ansible
       ```
       
       ![t128](asset2/t128.png)
       
       
       
       
       
       
       
       
       
     - Buat file hosts yang berisi:
       ![t129](asset2/t129.png)
       

       ```bash
       [php5]
       debian_php5 ansible_host=lxc_php5.dev ansible_ssh_user=root ansible_become_pass=a
       
       [php7]
       ubuntu_landing ansible_host=lxc_landing.dev ansible_ssh_user=root ansible_become_pass=a
       ubuntu_php7 ansible_host=lxc_php7.dev ansible_ssh_user=root ansible_become_pass=a
       
       [database]
       lxc_mariadb ansible_host=lxc_mariadb.dev ansible_ssh_user=root ansible_become_pass=a
       ```
       
       ![t130](asset2/t130.png)

     - Membuat install-mariadb.yml, yang berisi
       ![t131](asset2/t131.png)
       

       ```yaml
       - hosts: database
         vars:
           username: 'admin'
           password: 'SysAdminSas0102'
         roles:
           - { role: db }
       ```
       ![t132](asset2/t132.png)

     - Disini kita akan membuat roles yang bernama `db` . roles ini berisi kumpulan task instalasi dan konfigurasi mariadb

       ```bash
       mkdir -p roles/db
       ```
       
       ![t133](asset2/t133.png)
       
     - Didalam folder `roles/db` terdapat beberapa folder, yakni `handlers`, `tasks` dan `templates`. Folder `handlers` berisi perintah perintah untuk menjalankan mariadb seperti restart, sedangkan folder `tasks` berisi script instalasi mariadb dan folder `templates` berisi template konfigurasi untuk mariadb.

       ```bash
       mkdir -p roles/db/handlers
       mkdir -p roles/db/tasks
       mkdir -p roles/db/templates
       ```

     - Membuat script instalasi pada folder `tasks`.

       ```bash
       cd roles/db/tasks
       nano main.yml
       ```
       
      ![t134](asset2/t134.png)
      ![t135](asset2/t135.png)
      
     - roles/db/tasks/main.yml akan berisi:
       Isinya copy sesuai modul
       ![t136](asset2/t136.png)

       ```yaml
       ---
       - name: delete apt chache
         become: yes
         become_user: root
         become_method: su
         command: rm -vf /var/lib/apt/lists/*
       
       - name: install mariadb
         become: yes
         become_user: root
         become_method: su
         apt: name={{ item }} state=latest update_cache=true
         with_items:
          - python
          - mariadb-server
          - python-mysqldb
          - python-pymysql
       
       - name: Stop MySQL
         service: name=mysqld state=stopped
         
       - name: set environment variables
         shell: systemctl set-environment MYSQLD_OPTS="--skip-grant-tables"
         
       - name: Start MySQL
         service: name=mysqld state=started
         
       - name: sql query
         command:  mysql -u root --execute="UPDATE mysql.user SET authentication_string = PASSWORD('{{ password }}') WHERE User = 'root' AND Host = 'localhost';"
         
       - name: sql query flush
         command:  mysql -u root --execute="FLUSH PRIVILEGES"
         
       - name: Stop MySQL
         service: name=mysqld state=stopped
         
       - name: unset environment variables
         shell: systemctl unset-environment MYSQLD_OPTS
         
       - name: Start MySQL
         service: name=mysqld state=started
         
       - name: Create user for mysql
         command:  mysql -u root --execute="CREATE USER IF NOT EXISTS '{{ username }}'@'localhost' IDENTIFIED BY '{{ password }}';"
       
       - name: GRANT ALL PRIVILEGES to user {{username}}
         command:  mysql -u root --execute="GRANT ALL PRIVILEGES ON * . * TO '{{ username }}'@'localhost';"
       
       - name: Create user for remote mysql
         command:  mysql -u root --execute="CREATE USER IF NOT EXISTS '{{ username }}'@'%' IDENTIFIED BY '{{ password }}';"
       
       - name: GRANT ALL PRIVILEGES to remote user {{username}}
         command:  mysql -u root --execute="GRANT ALL PRIVILEGES ON * . * TO '{{ username }}'@'%';"
         
       - name: sql query flush
         command:  mysql -u root --execute="FLUSH PRIVILEGES"
         
       - name: Create DB Landing
         command:  mysql -u root --execute="CREATE DATABASE IF NOT EXISTS `landing`;"
       
       - name: Create DB blog
         command:  mysql -u root --execute="CREATE DATABASE IF NOT EXISTS `blog`;"
       
       - name: Create DB app
         command:  mysql -u root --execute="CREATE DATABASE IF NOT EXISTS `app`;"
       
       - name: Copy .my.cnf file with root password credentials
         template: 
           src=templates/my.cnf 
           dest=/etc/mysql/mariadb.conf.d/50-server.cnf
         notify: restart mysql
       ```
       
     - roles/db/templates/my.cnf akan berisi:
       ![t137](asset2/t137.png)
       
       Copy paste dari modul saja
       ![t138](asset2/t138.png)
       
     
       ```ini
       #
       # These groups are read by MariaDB server.
       # Use it for options that only the server (but not clients) should see
       #
       # See the examples of server my.cnf files in /usr/share/mysql/
       #
       
       # this is read by the standalone daemon and embedded servers
       [server]
       
       # this is only for the mysqld standalone daemon
       [mysqld]
       
       #
       # * Basic Settings
       #
       user		= mysql
       pid-file	= /var/run/mysqld/mysqld.pid
       socket		= /var/run/mysqld/mysqld.sock
       port		= 3306
       basedir		= /usr
       datadir		= /var/lib/mysql
       tmpdir		= /tmp
       lc-messages-dir	= /usr/share/mysql
       skip-external-locking
       
       # Instead of skip-networking the default is now to listen only on
       # localhost which is more compatible and is not less secure.
       bind-address		= 0.0.0.0
       
       #
       # * Fine Tuning
       #
       key_buffer_size		= 16M
       max_allowed_packet	= 16M
       thread_stack		= 192K
       thread_cache_size       = 8
       # This replaces the startup script and checks MyISAM tables if needed
       # the first time they are touched
       myisam_recover_options  = BACKUP
       #max_connections        = 100
       #table_cache            = 64
       #thread_concurrency     = 10
       
       #
       # * Query Cache Configuration
       #
       query_cache_limit	= 1M
       query_cache_size        = 16M
       
       #
       # * Logging and Replication
       #
       # Both location gets rotated by the cronjob.
       # Be aware that this log type is a performance killer.
       # As of 5.1 you can enable the log at runtime!
       #general_log_file        = /var/log/mysql/mysql.log
       #general_log             = 1
       #
       # Error log - should be very few entries.
       #
       log_error = /var/log/mysql/error.log
       #
       # Enable the slow query log to see queries with especially long duration
       #slow_query_log_file	= /var/log/mysql/mariadb-slow.log
       #long_query_time = 10
       #log_slow_rate_limit	= 1000
       #log_slow_verbosity	= query_plan
       #log-queries-not-using-indexes
       #
       # The following can be used as easy to replay backup logs or for replication.
       # note: if you are setting up a replication slave, see README.Debian about
       #       other settings you may need to change.
       #server-id		= 1
       #log_bin			= /var/log/mysql/mysql-bin.log
       expire_logs_days	= 10
       max_binlog_size   = 100M
       #binlog_do_db		= include_database_name
       #binlog_ignore_db	= exclude_database_name
       
       #
       # * InnoDB
       #
       # InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
       # Read the manual for more InnoDB related options. There are many!
       
       #
       # * Security Features
       #
       # Read the manual, too, if you want chroot!
       # chroot = /var/lib/mysql/
       #
       # For generating SSL certificates you can use for example the GUI tool "tinyca".
       #
       # ssl-ca=/etc/mysql/cacert.pem
       # ssl-cert=/etc/mysql/server-cert.pem
       # ssl-key=/etc/mysql/server-key.pem
       #
       # Accept only connections using the latest and most secure TLS protocol version.
       # ..when MariaDB is compiled with OpenSSL:
       # ssl-cipher=TLSv1.2
       # ..when MariaDB is compiled with YaSSL (default in Debian):
       # ssl=on
       
       #
       # * Character sets
       #
       # MySQL/MariaDB default is Latin1, but in Debian we rather default to the full
       # utf8 4-byte character set. See also client.cnf
       #
       character-set-server  = utf8mb4
       collation-server      = utf8mb4_general_ci
       
       #
       # * Unix socket authentication plugin is built-in since 10.0.22-6
       #
       # Needed so the root database user can authenticate without a password but
       # only when running as the unix root user.
       #
       # Also available for other users if required.
       # See https://mariadb.com/kb/en/unix_socket-authentication-plugin/
       
       # this is only for embedded server
       [embedded]
       
       # This group is only read by MariaDB servers, not by MySQL.
       # If you use the same .cnf file for MySQL and MariaDB,
       # you can put MariaDB-only options here
       [mariadb]
       
       # This group is only read by MariaDB-10.1 servers.
       # If you use the same .cnf file for MariaDB of different versions,
       # use this group for options that older servers don't understand
       [mariadb-10.1]
       ```
     
     - roles/db/handlers/main.yml akan berisi:
       ![t139](asset2/t139.png)
       ![t140](asset2/t140.png)
     
       ```yaml
       ---
       - name: restart mysql
         become: yes
         become_user: root
         become_method: su
         action: service name=mysql state=restarted
       ```
     
     - Jalankan perintah
            
       ```bash
       cd ~/ansible/modul2-ansible
       ansible-playbook -i hosts install-mariadb.yml -k
       ```
     
       Setelah itu exit dari handlers dan masuk ke ansible saja
       ![t141](asset2/t141.png)
       
       Berhasil—
       ![t142](asset2/t142.png)
       ![t143](asset2/t143.png)


       

     - Check apakah db sudah terinstall
     
       ```bash
       ssh root@lxc_mariadb.dev
       
       mysql -u admin -p
       show databases;
       ```
     
       Pass root mariadb.dev : 100
       Pass my sql mariadb : SysAdminSas0102
       ![t144](asset2/t144.png)
       ![t145](asset2/t145.png)
     
   - Setup ansible nginx untuk mengakses phpmyadmin
   
     - Edit install-mariadb.yml menjadi :
   
       ```bash
       - hosts: database
         vars:
           username: 'admin'
           password: 'SysAdminSas0102'
           domain: 'lxc_mariadb.dev'
         roles:
           - db
           - pma
       ```
       
       edit `nano install-mariadb.yml`
       ![t146](asset2/t146.png)

       Before
       ![t147](asset2/t147.png)
       
       After
       ![t148](asset2/t148.png)
 


     - Disini kita akan membuat roles yang bernama `pma` . roles ini berisi kumpulan task instalasi dan konfigurasi phpmyadmin
   
       ```bash
       mkdir -p roles/pma
       ```
   
     - Didalam folder `roles/pma` terdapat beberapa folder, yakni `handlers`, `tasks` dan `templates`. Folder `handlers` berisi perintah perintah untuk menjalankan nginx dan php seperti restart, sedangkan folder `tasks` berisi script instalasi phpmyadmin dan folder `templates` berisi template konfigurasi untuk nginx.
   
       ```bash
       mkdir -p roles/pma/tasks
       mkdir -p roles/pma/handlers
       mkdir -p roles/pma/templates
       ```
   
      ![t149](asset2/t149.png)
      
     - roles/pma/tasks/main.yml akan berisi:
       ![t150](asset2/t150.png)
       
       Isi copy paste sesuai modul
       ![t151](asset2/t151.png)

   
       ```yaml
       ---
       - name: delete apt chache
         become: yes
         become_user: root
         become_method: su
         command: rm -vf /var/lib/apt/lists/*
       
       - name: install nginx phpmyadmin
         become: yes
         become_user: root
         become_method: su
         apt: name={{ item }} state=latest update_cache=true
         with_items:
           - curl
           - nginx
           - nginx-extras
           - php7.2-fpm
           - php-mbstring
           - php-zip
           - php-gd
           - php-json
           - php-curl
           - phpmyadmin
       
       - name: enable module php mbstring
         command: phpenmod mbstring
         notify:
           - restart php
       
       - name: Copy pma.local
         template:
           src=templates/pma.local
           dest=/etc/nginx/sites-available/{{ domain }}
         vars:
           servername: '{{ domain }}'
       
       - name: Symlink pma.local
         command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
         notify:
           - stop apache2
           - restart nginx
       
       - name: Write {{ domain }} to /etc/hosts
         lineinfile:
           dest: /etc/hosts
           regexp: '.*{{ domain }}$'
           line: "127.0.0.1 {{ domain }}"
           state: present
       ```
       
     - roles/pma/templates/pma.local akan berisi:
       ![t152](asset2/t152.png)
       ![t153](asset2/t153.png)
     
     ```ini
     server {
         listen 80;
     
         server_name {{servername}};
     
         root /usr/share/phpmyadmin;
     
         index index.php;
     
         location / {
     
              try_files $uri $uri/ @phpmyadmin;
     
      }
     
      location @phpmyadmin {
                 fastcgi_pass unix:/run/php/php7.2-fpm.sock;   #Sesuaikan dengan versi PHP
     
                 fastcgi_param SCRIPT_FILENAME /usr/share/phpmyadmin/index.php;
     
                 include /etc/nginx/fastcgi_params;
     
                 fastcgi_param SCRIPT_NAME /index.php;
     
         }
     
         location ~ \.php$ {
     
                 fastcgi_pass unix:/run/php/php7.2-fpm.sock;  #Sesuaikan dengan versi PHP
     
                 fastcgi_index index.php;
     
                 fastcgi_param SCRIPT_FILENAME /usr/share/phpmyadmin$fastcgi_script_name;
     
                 include fastcgi_params;
     
         }
     }
     ```
     
     - roles/pma/handlers/main.yml akan berisi:
       ![t154](asset2/t154.png)
       ![t155](asset2/t155.png)
     
     ```yaml
       ---
       - name: stop apache2
         become: yes
         become_user: root
         become_method: su
         action: service name=apache2 state=stopped
       
       - name: restart nginx
         become: yes
         become_user: root
         become_method: su
         action: service name=nginx state=restarted
       
       - name: restart php
         become: yes
         become_user: root
         become_method: su
         action: service name=php7.2-fpm state=restarted
       
     ```
     
     - Jalankan perintah:
       ![t156](asset2/t156.png)
     
     ```bash
     cd ~/ansible/modul2-ansible
     ansible-playbook -i hosts install-mariadb.yml -k
     ```
       ![t157](asset2/t157.png)
     
     - edit /etc/nginx/sites-available/vm.local menjadi:
        ![t158](asset2/t158.png)
        
        Before
        ![t159](asset2/t159.png)

        After
        ![t160](asset2/t160.png)
        ![t161](asset2/t161.png)
        
        

     ```ini
        server {
             listen 80;
               listen [::]:80;
       
               server_name vm.local;
       
               root /var/www/html;
               index index.html;
       
               location /app {
                       rewrite /app/?(.*)$ /$1 break;
                       proxy_pass http://lxc_php5.dev;
               }
       
               location /blog {
                       rewrite /blog/?(.*)$ /$1 break;
                       proxy_pass http://lxc_php7.dev;
               }
       
               location /phpmyadmin {
                       rewrite /phpmyadmin/?(.*)$ /$1 break;
                       proxy_pass http://lxc_mariadb.dev;
               }
               
               location / {
                       #rewrite /php7/?(.*)$ /$1 break;
                       proxy_pass http://lxc_landing.dev;
               }
       
       }
     ```
     
     - coba akses vm.local/phpmyadmin/:

       ![t162](asset2/t162.png)
       ![t163](asset2/t163.png)
       ![t164](asset2/t164.png)
     
   
2. Install codeigniter pada lxc_php5.dev dengan beberapa requirement, yakni:

   - Menggunakan PHP5, dikonfigurasikan dengan ansible dan konfigurasikan ansible untuk menginstal php5.6 digroup php5

   - Clone Codeigniter dari https://github.com/aldonesia/sas-ci

   - Menggunakan database app yang terdapat pada lxc_mariadb

     Jawab

     - Buat file deploy-app.yml

      ![t2](asset2/t2.png)

       ```
       - hosts: php5
         vars:
           git_url: 'https://github.com/aldonesia/sas-ci'
           destdir: '/var/www/html/ci'
           domain: 'lxc_php5.dev'
         roles:
           - app
       ```
       
       
       ![t22](asset2/t22.png)

     - Disini kita akan membuat roles yang bernama `app` . roles ini berisi kumpulan task instalasi dan konfigurasi phpmyadmin
        
       ![t23](asset2/t23.png)
       
     - Didalam folder `roles/app` terdapat beberapa folder, yakni `handlers`, `tasks` dan `templates`. Folder `handlers` berisi perintah perintah untuk menjalankan nginx dan php seperti restart, sedangkan folder `tasks` berisi script instalasi phpmyadmin dan folder `templates` berisi template konfigurasi untuk nginx.

       ```bash
       mkdir -p roles/app/tasks
       mkdir -p roles/app/handlers
       mkdir -p roles/app/templates
       ```

     - roles/app/tasks/main.yml akan berisi:
     
       ![t24](asset2/t24.png)

       ```
       ---
       - name: delete apt chache
         become: yes
         become_user: root
         become_method: su
         command: rm -vf /var/lib/apt/lists/*
       
       - name: install requirement dpkg to install php5
         become: yes
         become_user: root
         become_method: su
         apt: name={{ item }} state=latest update_cache=true
         with_items:
           - ca-certificates
           - apt-transport-https
           - wget
           - curl
           - python-apt
           - software-properties-common
           - git
       
       - name: Add key
         apt_key:
           url: https://packages.sury.org/php/apt.gpg
           state: present
       
       - name: Add Php Repository
         apt_repository:
             repo: "deb https://packages.sury.org/php/ stretch main"
             state: present
             filename: php.list
             update_cache: true
       
       - name: install nginx php5
         become: yes
         become_user: root
         become_method: su
         apt: name={{ item }} state=latest update_cache=true
         with_items:
           - nginx
           - nginx-extras
           - php5.6
           - php5.6-fpm
           - php5.6-common
           - php5.6-cli
           - php5.6-curl
           - php5.6-mbstring
           - php5.6-mysqlnd
           - php5.6-xml
       
       - name: Git clone repo sas-ci
         become: yes
         git:
           repo: '{{ git_url }}'
           dest: "{{ destdir }}"
       
       - name: Copy app.conf
         template:
           src=templates/app.conf
           dest=/etc/nginx/sites-available/{{ domain }}
         vars:
           servername: '{{ domain }}'
       
       - name: Delete another nginx config
         become: yes
         become_user: root
         become_method: su
         command: rm -f /etc/nginx/sites-enabled/*
       
       - name: Symlink app.conf
         command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
         notify:
           - restart nginx
       
       - name: Write {{ domain }} to /etc/hosts
         lineinfile:
           dest: /etc/hosts
           regexp: '.*{{ domain }}$'
           line: "127.0.0.1 {{ domain }}"
           state: present
       ```

     - roles/app/templates/app.conf akan berisi:

       ![t25](asset2/t25.png)
       ![t26](asset2/t26.png)

       ```ini
       server {
         listen 80;
         server_name {{servername}};
         root {{ destdir }};
         index index.php;
         location / {
            try_files $uri $uri/ /index.php?$query_string;
         }
         location ~ \.php$ {
            fastcgi_pass unix:/run/php/php5.6-fpm.sock;  #Sesuaikan dengan versi PHP
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME {{ destdir }}$fastcgi_script_name;
            include fastcgi_params;
         }
       }
       ```

     - roles/app/handlers/main.yml akan berisi:
        
       ![t27](asset2/t27.png)
       ![t28](asset2/t28.png)

       ```
       ---
       - name: restart nginx
         become: yes
         become_user: root
         become_method: su
         action: service name=nginx state=restarted
        
       - name: restart php
         become: yes
         become_user: root
         become_method: su
         action: service name=php5.6-fpm state=restarted
       ```

     - Jalankan perintah

       ![t29](asset2/t29.png)

       ```
       ansible-playbook -i hosts deploy-app.yml -k
       ```

       ![t210](asset2/t210.png)
       ![t211](asset2/t211.png)
       
       
       tampilan browser
       ![t212](asset2/t212.png)
       



