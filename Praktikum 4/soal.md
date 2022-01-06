# Modul 4 - Web Server, Load Balancing dan uji performansi

Kelompok 7
Nama Anggota :
1. Rahmadina Oktaviana (1202190016)
2. Riska Aprilia (1202190007)
---


## Soal Modul 4
- Pada latihan kita sudah melakukan konfigurasi pada ubuntu_landing
- Maka Step berikutnya yaitu melakukan konfigurasi yang sama terhadap ubuntu php7.4 dan debian_php5.6 yang akan dilaporkan sebagai berikut :

### Load Balancing
1. Siapkan LXC untuk ubuntu7.4
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
		
		![2](assets/2.png)
		
		ganti ip menjadi 10.0.3.111
		
		![3](assets/3.png)
		
		terapkan konfigurasi netplan baru
		
		```sh
		netplan apply
		```
		
		check ip
		
		```sh
		ip addr show eth0
		```
		
		![4](assets/4.png)
		
		Daftarkan domain lxc_php7_2.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![5](assets/5.png)
		
		Konfigurasi nginx untuk lxc_php7.4_2.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_php7.dev
		```
		
		![6](assets/6.png)
		
		Check configurasi nginx dan start nginx
		
		```sh
		nginx -t
		service nginx restart
		```
		
		Coba akses lxc_php7
		
		```sh
		curl -i http://lxc_php7_2.dev
		```
		
		![7](assets/7.png)
		
		keluar dari ubuntu_landing_2
		
		```sh
		exit
		```
		
		![8](assets/8.png)
		
	* Masuk ke lxc ubuntu_php7.4_3
	
		```sh
		sudo lxc-attach -n ubuntu_php7.4_3
		```
		
	* Configurasi IP dan nginx ubuntu_php7.4_3
	
		```sh
		nano /etc/netplan/10-lxc.yaml
		```
		
		![9](assets/9.png)
		
		ganti ip menjadi 10.0.3.121
		
		![10](assets/10.png)
		
		terapkan konfigurasi netplan baru
		
		```sh
		netplan apply
		```
		
		check ip
		
		```sh
		ip addr show eth0
		```
		
		![11](assets/11.png)
		
		Daftarkan domain lxc_php7_3.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![12](assets/12.png)
		
		Konfigurasi nginx untuk lxc_php7_3.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_php7.dev
		```
		
		![13](assets/13.png)
		
		Check configurasi nginx dan start nginx
		
		```sh
		nginx -t
		service nginx restart
		```
		
		Coba akses lxc_php7
		
		```sh
		curl -i http://lxc_php7_3.dev
		```
		
		![14](assets/14.png)
		
		exit dari ubuntu_php7.4_3
		
		```sh
		exit
		```
		![15](assets/15.png)
		
2. Siapkan LXC untuk debian_php5.6
	* Clone LXC debian_php5.6 menjadi debian_php5.6_2 dan debian_php5.6_3
	
		```sh
		sudo lxc-stop -n debian_php5.6
		sudo lxc-copy -n debian_php5.6 -N debian_php5.6_2 -sKD
		sudo lxc-copy -n debian_php5.6 -N debian_php5.6_3 -sKD
		```
		
		![16](assets/16.png)
		
	* Start LXC 
	
		```sh
		sudo lxc-start -n debian_php5.6
		sudo lxc-start -n debian_php5.6_2
		sudo lxc-start -n debian_php5.6_3
		```
		
		![17](assets/17.png)
		![18](assets/18.png)
		
	* Masuk ke lxc debian_php5.6_2
	
		```sh
		sudo lxc-attach -n debian_php5.6_2
		```
		
	* Configurasi IP dan nginx debian_php5.6_2
	
		```sh
		nano /etc/network/interfaces
		```
		
		![19](assets/19.png)
		
		ganti ip menjadi 10.0.3.112
		
		![20](assets/20.png)
		
		
		Daftarkan domain lxc_php5_2.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![21](assets/21.png)
		
		Konfigurasi nginx untuk lxc_php5.6_2.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_php5.dev
		```
		
		![22](assets/22.png)
		
				
		keluar dari debian_php5.6_2
		
		```sh
		exit
		```
		
		![23](assets/23.png)
		
	* Masuk ke lxc debian_php5.6_3
	
		```sh
		sudo lxc-attach -n debian_php5.6_3
		```
		
	* Configurasi IP dan nginx debian_php5.6_3
	
		```sh
		nano /etc/network/interfaces
		```
		
		![24](assets/24.png)
		
		ganti ip menjadi 10.0.3.122
		
		![25](assets/25.png)
		
			
		Daftarkan domain lxc_php5_3.dev di hosts file
		
		```sh
		nano /etc/hosts
		```
		
		![26](assets/26.png)
		
		Konfigurasi nginx untuk lxc_php5.6_3.dev
		
		```sh
		nano /etc/nginx/sites-available/lxc_php5.dev
		```
		
		![27](assets/27.png)
		
		exit dari debian_php5.6_3
		
		```sh
		exit
		```
		![28](assets/28.png)

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
	
	* etc host
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
	![44](assets/44.png)

	* Konfigurasi load balancer menggunakan round robin untuk halaman landing vm.local pada nginx
	
		```sh
		sudo nano /etc/nginx/sites-available/vm.local
		```
		
		
		![45](assets/45.png)
		![46](assets/46.png)
		![47](assets/47.png)
		
	* Jalankan kembali jmeter
	
	
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
