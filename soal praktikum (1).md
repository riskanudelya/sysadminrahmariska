# Server Administration System - Practicum Module 1 Virtualization
___
### Report by :
Riska Aprilia       (1202190007)

Rahmadina Oktaviana (1202190016)
___
## Keadaan

Gilang dan Adit adalah sysadmin junior pada suatu perusahaan. Perusahaan tersebut akan membuat sebuah website company profile. Dimana website tersebut berisi 3 hal, yaitu company profile, blog dan aplikasi. CTO menjelaskan strategi domain yang akan digunakan, yaitu:

- vm.local
  - berisi landing page
- vm.local/blog
  - berisi blog
- vm.local/app
  - berisi aplikasi perusahaan

Setelah mendengarkan strategi domain dari CTO, Mereka mencoba membuat virtualisasi sederhana dengan skema dibawah ini :

![topologi](assets/topologi.png)

Dari skema diatas, mereka mementukan bahwa lxc_php5.dev akan digunakan sebagai server aplikasi dan lxc_php7.dev akan digunakan sebagai server blog dan vm.local digunakan sebagai proxy server serta untuk web landing page. Lantas, mereka bertemu dengan tim programmer. Tim Programmer memberi tahu kepada mereka bahwa ada perubahan baru baru ini terkait ubuntu 16.04.

![news](assets/news.png)

Karena hal ini, mereka berdiskusi dengan tim programmer dan menghasilkan suatu keputusan dimana skema yang akan dibuat adalah:

![soal_praktikum](assets/soal_praktikum.png)

## Soal Praktikum

Dari skema diatas maka mereka akan melakukan pekerjaan sebagai berikut :

**1. Rename ubuntu_php5.6 to ubuntu_landing, and change the IP following the new scheme** 

   (Rename ubuntu_php5.6 menjadi ubuntu_landing, serta rubah IP mengikuti skema yang baru)
   * #### Langkah Pengerjaan Soal Praktikum
    a. menampilkan container tersedia sebelum melakukan rename ubuntu_php5.6


   ```bash
   sudo lxc-ls -f
   ```
    b. stop ubuntu_php5.6 sebelum melakukan rename, pastikan sudah dalam kondisi stopped dengan cek kembali list container
   ```bash
   lxc-stop ubuntu_php5.6
   lxc-ls -f
   ```
   *tampilan proses*
   ![s11](assets/s11.png)
   
   c. merename ubuntu_php5.6 menjadi ubuntu_landing kemudian cek kembali list container yang tersedia
   ```bash
   sudo lxc-copy -R -n ubuntu_php5.6 -N ubuntu_landing
   lxc-ls -f
   ```
   *tampilan ubuntu_php5.6 telah terganti menjadi ubuntu_landing*

   ![s12](assets/s12.png)

   **ubuntu_landing**

   d. start ubuntu_landing dan masuk ke attach container pada lxc
   ```bash
   sudo lxc-start -n ubuntu_landing
   lxc-attach -n ubuntu_landing
   ```
   ![s13](assets/s13.png)
   
   e. set static IP
   ```bash
   nano /etc/network/interfaces
   ```
   
   Ganti IP address menjadi 10.0.3.103 sesuai konsep baru topologi jaringan untuk ubuntu_landing
   ![s14](assets/s14.png)

   f. Lakukan restart setelah selesai mengubah IP apakah IP yang tadinya 102 sudah menjadi 103, jika belum maka lakukan perintah shutdown now, kemudian start kembali ubuntu landing dan masuk ke attach container dan cek ip kembali

   ```bash
   systemctl restart networking.service 
   atau
   shutdown now
   sudo lxc-start -n ubuntu_landing
   sudo lxc-attach -n ubuntu_landing
   ifconfig
   ```
   ![s15](assets/s15.png)

**2. Install lxc debian 9 with the name debian_php5.6** 
  
   (Install lxc debian 9 dengan nama debian_php5.6)
  
   Bisa mengecek list container terlebih dahulu sebelum menginstall lxc debian 9 dengan nama debian_php5.6 agar dapat terlihat perbedaannya (pre n post)
   
   ![s21](assets/s21.png)
   a. mengganti *sources list* ubuntu
   
   ![s22](assets/s22.png)
   
   b. dengan memasukkan directory debian 9
   deb http://archive.debian.org/debian-non-US/ main contrib non-free
   deb-src http://archive.debian.org/debian-non-US/ main contrib non-free

   ![s23](assets/s23.png)

   c. download lxc debian dan buat lxc nama-nya menjadi debian_php5.6
   ```bash
   sudo lxc-create -n debian_php5.6 -t download -- --dist debian --release stretch --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
   ```      
   ![s24](assets/s24.png)
   ![s25](assets/s25.png)

   d. jika sudah install maka lihat info status debian_5.6
   ```bash
   sudo lxc-info -n debian_php5.6
   ```
   Jika kondisi stopped maka start dengan cara
   ```bash
   lxc-start -n debian_php5.6
   ```
   ![s26](assets/s26.png)
 
   lakukan update dan upgrade
   ```bash
   sudo apt update; sudo apt upgrade -y
   ```
   ![s27](assets/s27.png)
   ![s28](assets/s28.png)

   cek list container untuk memastikan daftar name lxc sesuai
   ![s29](assets/s29.png)


**3. Setup nginx on debian_php5.6 for the http://lxc_php5.dev domain, create an index.html page that describes the lxc name information** 

   (setup nginx pada debian_php5.6 untuk domain http://lxc_php5.dev , buat halaman index.html yang menerangkan informasi nama lxc)
   
   a. install nginx dan nginx-extras di lxc debian_php5.6

 
   ```bash
   sudo apt install nginx nginx-extras
   ```
   ![s31](assets/s31.png)

   b. set static IP
   ```bash
   apt install nano net-tools curl
   ```
   ![s32](assets/s32.png)
   c. setting ip address menjadi 10.0.3.102
   ```bash
   nano /etc/network/interfaces
   ```
   ![s33](assets/s33.png)
   d. lakukan perintah shutdown agar ip lebih cepat terdeteksi setelah diganti menjadi 102, kemudian start kembali dan masuk ke attach container, kemudian ifconfig

   ```bash
   shutdown now
   sudo lxc-start -n debian.php5.6
   sudo lxc-attach -n debian.php5.6
   ifconfig
   ```
   ![s34](assets/s34.png)

**4. Setup nginx on ubuntu_landing for the http://lxc_landing.dev domain, create an index.html page that describes the lxc name information** 
     (setup nginx pada ubuntu_landing untuk domain http://lxc_landing.dev , buat halaman index.html yang menerangkan informasi nama lxc)
  
  a. Exit debian_php5.6 and start ubuntu landing. Go to sites-available and edit lxc_php5.6.dev
  
    ```bash
    sudo lxc-start -n ubuntu_landing
    sudo lxc-attach -n ubuntu_landing
    cd ../sites-available
    nano lxc_php5.6.dev
    ```
   ![s51](assets/s51.png)

   b. Edit server name di lxc_php5.6.dev 

   ![s52](assets/s52.png)

   c. Go to directory sites-enabled, test nginx and reload nginx
   
    ```bash
    cd ../sites-enabled
    ls -la
    nginx -t
    nginx -s reload
    ```
   ![s53](assets/s53.png)
  
   d. Setting hosts directory /etc/hosts 

   ![s54](assets/s54.png)

   e. Go to directory html and check 
   
    ```bash
    cd /var/www/html
    ls
    cd /var/www/html/lxc_php5.6/
    ```
   ![s55](assets/s55.png)

   f. Edit index.html 'Halaman ini dari lxc ubuntu_landing'

   ![s59](assets/s59.png)

   g. Check (http://lxc_landing.dev) pada localhost lxc ubuntu_landing menggunakan curl
   
    ```bash
    curl -i http://lxc_landing.dev
    ```
   ![s58](assets/s58.png)

**5. LXC ubuntu_landing should auto start when the vm is started, this is used to keep the company profile website from experiencing downtime**
   
   a. Exit from directory lxc ubuntu_landing
   
    ```bash
    exit
    ```
   ![s61](assets/s61.png)
   
   b. Stop and check status ubuntu_landing
   
    ```bash
    sudo lxc-stop -n ubuntu_landing
    lxc-ls -f
    ```
    ![s62](assets/s62.png)
    
    c. Go in directory /var/lib/lxc and directory ubuntu_landing/config
    
    ```bash
    cd /vsr/lib/lxc
    ls
    cd ubuntu_landing
    ls
    nano config
    ```
   ![s63](assets/s63.png)
   ![s64](assets/s64.png)
   
   d. Autostart 1
   
    ```bash
    lxc.start.auto = 1
    ```
   ![s65](assets/s65.png)
   
   e. Exit and check
   
    ```bash
    exit
    sudo lxc-ls -f
    ```
   ![s66](assets/s66.png)

**6. Setup nginx on vm.local to set proxy_pass where :**
   
   **a. Setting hosts**
   
    ```bash
    sudo nano /etc/hosts
    ```
   ![s71](assets/s71.png)
   ![s72](assets/s72.png)
   
  **b. Go in directory sites-available and vm.local**
  
    ```bash
    cd /etc/nginx/sites-available
    ls
    sudo nano vm.local
    ```
   ![s73](assets/s73.png)
   ![s75](assets/s75.png)
    - accessing http://vm.local will redirect to http://lxc_landing.dev :
    
    ```bash
    location / {
        rewrite /?(.*)$ /$1 break;
        proxy_pass http://lxc_landing.dev;
    }
    ```

    - accessing http://vm.local/blog will redirect to http://lxc_php7.dev :
    
    ```bash
    location /blog {
        rewrite /blog/?(.*)$ /$1 break;
        proxy_pass http://lxc_php7.dev
    }
    ```
  
    - accessing http://vm.local/app will redirect to http://lxc_php5.dev :
    
    ```bash
    location /app {
        rewrite /app/?(.*)$ /$1 break;
        proxy_pass http://lxc_php5.dev;
    }
    ```
   
   **c. Go in sites-enabled reset nginx**
   
    ```bash
    cd ../sites-enabled
    ls -la
    sudo nginx -t
    sudo nginx -s reload
    ```
    ![s76](assets/s76.png)

**7. Access all three urls**

   a. Test and check curl -i
   
   ```bash
   curl -i http://vm.local/
   curl -i http://vm.local/app
   curl -i http://vm.local/blog
   ```
   
   b. access http://vm.local/blog

   ![s79](assets/s79.png)

   c. access http://vm.local/app

   ![s710](assets/s710.png)

   d. access http://vm.local/

   ![s711](assets/s711.png)
  
   e. Konfigurasi ip hosts 
  
   ![s712](assets/s712.png)

**8. Analisa**
   
   - Why for php5.6 needs can't use ubuntu 16.04, so it needs to change the os to debian 9?
      - In April 2021, Ubuntu 16.04 Xenial will reach End of Standart Support and will be available only as a paid option through Ubuntu Extended Security Maintenance.
   - Why use LXC virtualization on the website schema that will be developed?
      - Because OS virtualization that allows us to run multiple Linux systems on one computer system at the same time. Of course this platform only applies to Linux only.
   - What is a proxy server? why can we think of vm.local as a proxy server?
     - A proxy server is a system that works as a network intermediary, for example when you access a website page, the proxy will request and receive information from the   website to the device you are using. There are different levels of security, information confidentiality, and functions depending on the type you use. Because we use it as a network intermediary between the virtual machine and localhost





