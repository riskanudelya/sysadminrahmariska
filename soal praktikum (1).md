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

![topologi](assets/topologi.PNG)

Dari skema diatas, mereka mementukan bahwa lxc_php5.dev akan digunakan sebagai server aplikasi dan lxc_php7.dev akan digunakan sebagai server blog dan vm.local digunakan sebagai proxy server serta untuk web landing page. Lantas, mereka bertemu dengan tim programmer. Tim Programmer memberi tahu kepada mereka bahwa ada perubahan baru baru ini terkait ubuntu 16.04.

![news](assets/news.PNG)

Karena hal ini, mereka berdiskusi dengan tim programmer dan menghasilkan suatu keputusan dimana skema yang akan dibuat adalah:

![soal_praktikum](assets/soal_praktikum.png)

## Soal Praktikum

Dari skema diatas maka mereka akan melakukan pekerjaan sebagai berikut :

1. Rename ubuntu_php5.6 to ubuntu_landing, and change the IP following the new scheme
   * #### Langkah Pengerjaan Soal Praktikum
    1. menampilkan container tersedia sebelum melakukan rename ubuntu_php5.6
    

   ```bash
   sudo lxc-ls -f
   lxc-stop ubuntu_php5.6
   lxc-ls -f
   ```
   ![s11](assets/s11.PNG)

2. Install lxc debian 9 with the name debian_php5.6
   * #### Langkah Pengerjaan Soal Praktikum

3. Setup nginx on debian_php5.6 for the http://lxc_php5.dev domain, create an index.html page that describes the lxc name information
   * #### Langkah Pengerjaan Soal Praktikum

4. Setup nginx on ubuntu_landing for the http://lxc_landing.dev domain, create an index.html page that describes the lxc name information
   * #### Langkah Pengerjaan Soal Praktikum

5. LXC ubuntu_landing should auto start when the vm is started, this is used to keep the company profile website from experiencing downtime
- Exit from directory lxc ubuntu_landing
    ```
    exit
    ```
   ![s61](assets/s61.PNG)
- Stop and check status ubuntu_landing
    ```
    sudo lxc-stop -n ubuntu_landing
    lxc-ls -f
    ```
    ![s62](assets/s62.PNG)

- Go in directory /var/lib/lxc and directory ubuntu_landing/config
    ```
    cd /vsr/lib/lxc
    ls
    cd ubuntu_landing
    ls
    nano config
    ```
   ![s63](assets/s63.PNG)
   ![s64](assets/s64.PNG)
  
- Autostart 1
    ```
    lxc.start.auto = 1
    ```
   ![s65](assets/s65.PNG)

- Exit and check
    ```
    exit
    sudo lxc-ls -f
    ```
   ![s66](assets/s66.PNG)

6. Setup nginx on vm.local to set proxy_pass where :
- Setting hosts
  ```
  sudo nano /etc/hosts
  ```
  ![s71](assets/s71.PNG)
  ![s72](assets/s72.PNG)

- Go in directory sites-available and vm.local
    ```
    cd /etc/nginx/sites-available
    ls
    sudo nano vm.local
    ```
   ![s73](assets/s73.PNG)
   ![s75](assets/s75.PNG)
   - accessing http://vm.local will redirect to http://lxc_landing.dev :
    ```
    location / {
        rewrite /?(.*)$ /$1 break;
        proxy_pass http://lxc_landing.dev;
    }
    ```

   - accessing http://vm.local/blog will redirect to http://lxc_php7.dev :
    ```
    location /blog {
        rewrite /blog/?(.*)$ /$1 break;
        proxy_pass http://lxc_php7.dev
    }
    ```
  
   - accessing http://vm.local/app will redirect to http://lxc_php5.dev :
    ```
    location /app {
        rewrite /app/?(.*)$ /$1 break;
        proxy_pass http://lxc_php5.dev;
    }
    ```

- Go in sites-enabled reset nginx
  ```
  cd ../sites-enabled
  ls -la
  sudo nginx -t
  sudo nginx -s reload
  ```
  ![s76](assets/s76.PNG)

7. Access all three urls
- Test and check curl -i
  ```
  curl -i http://vm.local/
  curl -i http://vm.local/app
  curl -i http://vm.local/blog
  ```

- access http://vm.local/blog

  ![s79](assets/s79.PNG)

- access http://vm.local/app

  ![s710](assets/s710.PNG)

- access http://vm.local/

  ![s711](assets/s711.PNG)
  
- Konfigurasi ip hosts 
  ![s712](assets/s712.PNG)


8. Analisa

   - Why for php5.6 needs can't use ubuntu 16.04, so it needs to change the os to debian 9?
      - In April 2021, Ubuntu 16.04 Xenial will reach End of Standart Support and will be available only as a paid option through Ubuntu Extended Security Maintenance.
   - Why use LXC virtualization on the website schema that will be developed?
      - Because OS virtualization that allows us to run multiple Linux systems on one computer system at the same time. Of course this platform only applies to Linux only.
   - What is a proxy server? why can we think of vm.local as a proxy server?
     - A proxy server is a system that works as a network intermediary, for example when you access a website page, the proxy will request and receive information from the website to the device you are using. There are different levels of security, information confidentiality, and functions depending on the type you use. Because we use it as a network intermediary between the virtual machine and localhost





