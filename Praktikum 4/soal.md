# Modul 4 - Web Server, Load Balancing dan uji performansi

## Dasar Teori

### Web Server
Web Server adalah perangkat yang menyediakan layanan akses kepada pengguna melalui protokol HTTP atau HTTPS melalui aplikasi web.

### Load balancing
Load balancing adalah suatu mekanisme penyeimbangan beban yang bekerja dengan cara membagi beban pekerjaan. Load balancer adalah aplikasi atau alat yang bertugas untuk melakukan load balancing . Load balancer dapat meggunakan berbagai macam algoritma load balancing yang bertujuan untuk membagi beban pekerjaan seadil-adilnya. Minimal arsitektur load balancing adalah sebagai berikut:

![loadbalancing](assets/loadbalancing.png)

#### Kenapa dibutuhkan load balancing?
Untuk menangani banyaknya pengguna yang mengakses layanan pada satu waktu dan menjaga layanan tetap tersedia setiap saat, dibutuhkan lebih dari satu komputer untuk memasang layanannya. Dengan layanan yang tersedia di banyak server, dibutuhkan mekanisme pembagian beban untuk memberikan beban yang seimbang pada setiap server. Dengan meletakkan layanan pada beberapa server dan pembagian beban yang optimal, setiap permintaan pengguna bisa ditangani dengan efisien.

#### Nginx Load Balancing
Load Balancing di beberapa instans aplikasi adalah teknik yang umum digunakan untuk mengoptimalkan pemanfaatan sumber daya, memaksimalkan throughput, mengurangi latensi, dan memastikan konfigurasi yang toleran terhadap kesalahan.

Dimungkinkan untuk menggunakan nginx sebagai Load Balancer HTTP yang sangat efisien untuk mendistribusikan lalu lintas ke beberapa server aplikasi dan untuk meningkatkan kinerja, skalabilitas, dan keandalan aplikasi web dengan nginx.

Mekanisme (atau metode) Load Balancing berikut didukung di nginx:
1. Round Robin

	permintaan ke server aplikasi didistribusikan secara round-robin
	
	```sh
	http {
	    upstream myapp1 {
	        server srv1.example.com;
	        server srv2.example.com;
	        server srv3.example.com;
	    }
	
	    server {
	        listen 80;
	
	        location / {
	            proxy_pass http://myapp1;
	        }
	    }
	}
	```

2. Least Connection

	permintaan berikutnya diberikan ke server dengan jumlah koneksi aktif paling sedikit
	
	```sh
	http {
	    upstream myapp1 {
	    	least_conn;
	        server srv1.example.com;
	        server srv2.example.com;
	        server srv3.example.com;
	    }
	
	    server {
	        listen 80;
	
	        location / {
	            proxy_pass http://myapp1;
	        }
	    }
	}
	```

3. Ip Hash

	fungsi hash digunakan untuk menentukan server apa yang harus dipilih untuk permintaan berikutnya (berdasarkan alamat IP klien).
	```sh
	http {
	    upstream myapp1 {
	    	ip_hash;
	        server srv1.example.com;
	        server srv2.example.com;
	        server srv3.example.com;
	    }
	
	    server {
	        listen 80;
	
	        location / {
	            proxy_pass http://myapp1;
	        }
	    }
	}
	```

4. Weighted Load Balancing

	Hal ini juga memungkinkan untuk mempengaruhi algoritma load balancing nginx lebih jauh dengan menggunakan bobot server.
	
	Dengan round-robin khususnya itu juga berarti distribusi permintaan yang kurang lebih sama di seluruh server — asalkan ada permintaan yang cukup, dan ketika permintaan diproses secara seragam dan diselesaikan dengan cukup cepat.
	
	Ketika parameter bobot ditentukan untuk server, bobot diperhitungkan sebagai bagian dari keputusan penyeimbangan beban.


	```sh
	http {
	    upstream myapp1 {
	        server srv1.example.com weight=3;
	        server srv2.example.com;
	        server srv3.example.com;
	    }
	
	    server {
	        listen 80;
	
	        location / {
	            proxy_pass http://myapp1;
	        }
	    }
	}
	```

### Uji Performansi
pengujian kinerja didefinisikan sebagai jenis pengujian perangkat lunak untuk memastikan aplikasi perangkat lunak yang akan bekerja dengan baik di bawah beban kerja yang diharapkan.

#### Load Testing
teknik performance testing dengan mengukur respon sistem dalam berbagai load condition. Penelitian ini membantu menentukan bagaimana software berperilaku ketika beberapa user mengakses software secara bersamaan

#### Stress Testing
Stress testing juga dikenal sebagai pengujian daya tahan untuk menentukan batas, di mana sistem atau perangkat lunak atau perangkat keras rusak. Hal ini juga untuk memastikan sistem berjalan efektif meskipun dalam kondisi ekstrem

### Apache JMeter
![Logo Apahce Jmeter](assets/Apache-JMeter.jpg)

Aplikasi Apache JMeter™ adalah perangkat lunak sumber terbuka, aplikasi Java murni 100% yang dirancang untuk memuat perilaku fungsional pengujian dan mengukur kinerja. Ini pada awalnya dirancang untuk menguji Aplikasi Web tetapi sejak itu diperluas ke fungsi pengujian lainnya.

## Latihan

### Load Balancing
1. Siapkan LXC untuk landing
	* Clone LXC ubuntu_php7.4 menjadi ubuntu_php7.4_2 dan ubuntu_php7.4_3
	
		```sh
		sudo lxc-stop -n ubuntu_php7.4
		sudo lxc-copy -n ubuntu_php7.4 -N ubuntu_php7.4_2 -sKD
		sudo lxc-copy -n ubuntu_php7.4 -N ubuntu_php7.4_3 -sKD
		```
		
		![lxc-clone result](assets/lxc-clone.png)
		
	* Start LXC 
	
		```sh
		sudo lxc-start -n ubuntu_php7.4
		sudo lxc-start -n ubuntu_php7.4_2
		sudo lxc-start -n ubuntu_php7.4_3
		```
		
		![1](assets/1.png)
		
	* Masuk ke lxc ubuntu_landing_2
	
		```sh
		sudo lxc-attach -n ubuntu_php7.4_2
		```
		
	* Configurasi IP dan nginx ubuntu_php7.4_2
	
		```sh
		nano /etc/netplan/10-lxc.yaml
		```
		
		![1](assets/1.png)
		
		ganti ip menjadi 10.0.3.111
		
		![2](assets/2.png)
		
		terapkan konfigurasi netplan baru
		
		```sh
		netplan apply
		```
		
		check ip
		
		```sh
		ip addr show eth0
		```
		
		![3](assets/3.png)
		
		Daftarkan domain lxc_landing_2.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![4](assets/4.png)
		
		Konfigurasi nginx untuk lxc_php7.4_2.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_php7.dev
		```
		
		![5](assets/5.png)
		
		Check configurasi nginx dan start nginx
		
		```sh
		nginx -t
		service nginx restart
		```
		
		Coba akses lxc_php7
		
		```sh
		curl -i http://lxc_php7_2.dev
		```
		
		![6](assets/6.png)
		
		keluar dari ubuntu_landing_2
		
		```sh
		exit
		```
		
		![7](assets/7.png)
		
	* Masuk ke lxc ubuntu_php7.4_3
	
		```sh
		sudo lxc-attach -n ubuntu_php7.4_3
		```
		
	* Configurasi IP dan nginx ubuntu_php7.4_3
	
		```sh
		nano /etc/netplan/10-lxc.yaml
		```
		
		![8](assets/8.png)
		
		ganti ip menjadi 10.0.3.121
		
		![9](assets/9.png)
		
		terapkan konfigurasi netplan baru
		
		```sh
		netplan apply
		```
		
		check ip
		
		```sh
		ip addr show eth0
		```
		
		![10](assets/10.png)
		
		Daftarkan domain lxc_php7_3.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![11](assets/11.png)
		
		Konfigurasi nginx untuk lxc_php7_3.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_landing.dev
		```
		
		![12](assets/12.png)
		
		Check configurasi nginx dan start nginx
		
		```sh
		nginx -t
		service nginx restart
		```
		
		Coba akses lxc_php7
		
		```sh
		curl -i http://lxc_php7_3.dev
		```
		
		![13](assets/13.png)
		
		exit dari ubuntu_landing_3
		
		```sh
		exit
		```
		![14](assets/14.png)
		
2. Setup Nginx

	![15](assets/15.png)
	![16](assets/16.png)
	![17](assets/17.png)
	![18](assets/18.png)
	![19](assets/19.png)
	![20](assets/20.png)
	![21](assets/21.png)
	![22](assets/22.png)
	![23](assets/23.png)
	![24](assets/24.png)
	![25](assets/25.png)
	![26](assets/26.png)
	![27](assets/27.png)
	![28](assets/28.png)
	![29](assets/29.png)
	![](assets/30.png)
	![](assets/31.png)
	![](assets/32.png)
	![](assets/33.png)
	![](assets/34.png)
	![](assets/35.png)
	![](assets/36.png)
	![](assets/37.png)
	![](assets/38.png)
	![](assets/39.png)
	![](assets/40.png)
	![](assets/41.png)
	![](assets/42.png)
	![](assets/43.png)

	* Konfigurasi load balancer menggunakan round robin untuk halaman landing vm.local pada nginx
	
		```sh
		sudo nano /etc/nginx/sites-available/vm.local
		```
		
		![44](assets/44.png)
		![45](assets/45.png)
		![46](assets/46.png)
		
	* Jalankan kembali jmeter
	
	![](assets/47.png)
	![](assets/48.png)
	![](assets/49.png)
	![](assets/50.png)
	![](assets/51.png)
	![](assets/52.png)
	![](assets/53.png)
	![](assets/54.png)
	![](assets/55.png)
	![](assets/56.png)
	![](assets/57.png)
	![](assets/58.png)
	![](assets/59.png)
	![](assets/60.png)
	![](assets/61.png)
	![](assets/62.png)
	![](assets/63.png)
	
	
## Soal Praktikum
1. Terapkan loadbalancer untuk /blog dan /app dengan ketentuan
	1. /blog menggunakan least_conn
	2. /app menggunakan ip hash
	3. disarakan menggunakan ansible untuk instalasi
2. Gunakan apache Jmeter untuk menganalisa perbedaan antara /, /app, /blog dengan loadbalancer dan tanpa loadbalancer pada traffic 50, 100 dan 150 users. Analisa dari segi waktu saja. Tulis langkah testing dan analisa dengan bahasa sendiri.

## Referensi
1. http://nginx.org/en/docs/http/load_balancing.html
2. https://jmeter.apache.org/
3. http://coding4ever.net/blog/2015/10/20/performance-test-menggunakan-jmeter/
4. https://www.youtube.com/watch?v=mXGcBvWYl-U
5. https://medium.com/doku-insight/jmeter-87cccc713733





![lxc-clone result](assets/lxc-clone.png)
![1](assets/1.png)
![2](assets/2.png)
![3](assets/3.png)
![4](assets/4.png)
![5](assets/5.png)
![6](assets/6.png)
![7](assets/7.png)
![8](assets/8.png)
![9](assets/9.png)
![10](assets/10.png)
![11](assets/11.png)
![12](assets/12.png)
![13](assets/13.png)
![14](assets/14.png)
![15](assets/15.png)
![16](assets/16.png)
![17](assets/17.png)
![18](assets/18.png)
![19](assets/19.png)
![20](assets/20.png)
![21](assets/21.png)
![22](assets/22.png)
![23](assets/23.png)
![24](assets/24.png)
![25](assets/25.png)
![26](assets/26.png)
![27](assets/27.png)
![28](assets/28.png)
![29](assets/29.png)
![](assets/30.png)
![](assets/31.png)
![](assets/32.png)
![](assets/33.png)
![](assets/34.png)
![](assets/35.png)
![](assets/36.png)
![](assets/37.png)
![](assets/38.png)
![](assets/39.png)
![](assets/40.png)
![](assets/41.png)
![](assets/42.png)
![](assets/43.png)
![](assets/44.png)
![](assets/45.png)
![](assets/46.png)
![](assets/47.png)
![](assets/48.png)
![](assets/49.png)
![](assets/50.png)
![](assets/51.png)
![](assets/52.png)
![](assets/53.png)
![](assets/54.png)
![](assets/55.png)
![](assets/56.png)
![](assets/57.png)
![](assets/58.png)
![](assets/59.png)
![](assets/60.png)
![](assets/61.png)
![](assets/62.png)
![](assets/63.png)
