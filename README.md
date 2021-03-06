# Jarkom_Modul5_Praktikum_A12

##### Oleh :
- Achmad Sofyan Pratama (05111840000061)
- Oktarizka Asviananda Nursanty (05111840000156)

# SOAL

Setelah kalian mempelajari semua modul yang telah diberikan, Bibah ingin meminta bantuan untuk
terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Bibah.
(A) Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan
Bibah seperti dibawah ini :

![topo](https://user-images.githubusercontent.com/62512432/103281361-30443380-4a05-11eb-9311-85e5228d56bd.png)

Keterangan : SURABAYA diberikan IP TUNTAP

MALANG merupakan DNS Server diberikan IP DMZ
MOJOKERTO merupakan DHCP Server diberikan IP DMZ
MADIUN dan PROBOLINGGO merupakan WEB Server
Setiap Server diberikan memory sebesar 128M
Client dan Router diberikan memori sebesar 96M
Jumlah host pada subnet SIDOARJO 200 Host
Jumlah host pada subnet GRESIK 210 Host

(B) karena kalian telah mempelajari Subnetting dan Routing, Bibah meminta kalian untuk membuat
topologi tersebut menggunakan teknik CIDR atau VLSM. Setelah melakukan subnetting, (C) kalian
juga diharuskan melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
(D) Tugas berikutnya adalah memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis
menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian
kalian mengingat bahwa kalian harus setting DHCP RELAY pada router yang menghubungkannya,
seperti yang kalian telah pelajari di masa lalu.
(1) Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi
SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan
MASQUERADE.

(2) Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server
yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.
(3) Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP
dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari
mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.
kemudian kalian diminta untuk membatasi akses ke MALANG yang berasal dari SUBNET
SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:
● (4) Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin
sampai Jumat.
● (5) Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap
harinya.
Selain itu paket akan di REJECT.
Karena kita memiliki 2 buah WEB Server, (6) Bibah ingin SURABAYA disetting sehingga setiap
request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada
PROBOLINGGO port 80 dan MADIUN port 80.
(7) Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap
UML yang memiliki aturan drop.
Bibah berterima kasih kepada kalian karena telah mau membantunya. Bibah juga mengingatkan agar
semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai
backup.

# JAWABAN

### A. Membuat topologi

Pada ```topologi5.sh``` diisi sesuai gambar topologi sebagai berikut :

![isitopo](https://user-images.githubusercontent.com/62512432/103284578-2d017580-4a0e-11eb-9676-6267e18392a6.png)

lalu kemudian di ```bash topologi5.sh``` untuk menjalankan.

### B. Subnetting VLSM

Selanjutnya melakukan subnetting dengan menggunakan metode VLSM. Didapat hasilnya sebagai berikut :

![vlsm](https://user-images.githubusercontent.com/62512432/103282874-a5196c80-4a09-11eb-87f0-31fa38d4c24d.png)

lalu didapatkan IP untuk masing-masing subnet (subnet Malang-Mojokerto-Batu tidak perlu diikutkan dalam pembagian IP karena mereka langsung mendapatkan IP DMZ)

![ip](https://user-images.githubusercontent.com/62512432/103282937-d4c87480-4a09-11eb-9430-a9a19f591d94.png)

setelah didapatkan IP masing-masing subnet, dilakukan konfigurasi pada setiap UML. Pertama pada SURABAYA, KEDIRI, dan BATU dilakukan uncomment pada perintah ```net.ipv4.ip_forward=1``` agar dapat meneruskan route nantinya. Hal ini dilakukan dengan cara mengetikkan ```nano /etc/sysctl.conf``` kemudian edit di situ, dan untuk mengaktifkan perubahan baru, mengetikkan ```sysctl -p```.

Selanjutnya, pada ```nano /etc/network/interfaces``` ditambahkan connfig seperti dibawah ini :

#### SURABAYA (Router)

![sby](https://user-images.githubusercontent.com/62512432/103284805-d8aac580-4a0e-11eb-8481-b3c98bd8f71b.png)

#### KEDIRI (Router & DHCP Relay)

![kdr](https://user-images.githubusercontent.com/62512432/103284845-f841ee00-4a0e-11eb-9144-7ffcff999168.png)

#### BATU (Router & DHCP Relay)

![bt](https://user-images.githubusercontent.com/62512432/103284881-10b20880-4a0f-11eb-914a-b54f43d2a584.png)

#### PROBOLINGGO (Web Server)

![prblgg](https://user-images.githubusercontent.com/62512432/103284924-33442180-4a0f-11eb-9ecc-792fb223f685.png)

#### MADIUN (Web Server)

![mdun](https://user-images.githubusercontent.com/62512432/103284945-45be5b00-4a0f-11eb-8f3c-62539e76bc67.png)

#### MALANG (DNS Server)

![mlg](https://user-images.githubusercontent.com/62512432/103285033-8cac5080-4a0f-11eb-8db5-9e2202ace9c4.png)

#### MOJOKERTO (DHCP Server)

![mojo](https://user-images.githubusercontent.com/62512432/103285003-7a321700-4a0f-11eb-8f80-670d33136be5.png)

Selanjutnya pada setiap UML dilakukan ```service networking restart```.

### C. Routing

Pada UML SURABAYA, KEDIRI, dan BATU ditambahkan proses routing sesuai dengan metode yang digunakan yaitu metode VLSM. Sehingga ditambahkan isi ```route.sh``` sebagai berikut :

#### SURABAYA

![rutsby](https://user-images.githubusercontent.com/62512432/103285115-ced59200-4a0f-11eb-8557-4acacd38c517.png)

#### KEDIRI

![rutkdr](https://user-images.githubusercontent.com/62512432/103285407-a732f980-4a10-11eb-86a0-1c8c13100e45.png)

#### BATU

![rutbt](https://user-images.githubusercontent.com/62512432/103285135-d8f79080-4a0f-11eb-933b-17348fd592f3.png)

kemudian agar bisa berjalan, lakukan ```iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.0.0/16``` terlebih dahulu pada 3 UML diatas, selanjutnya lakukan ```source route.sh``` untuk menjalankan ```route.sh```. Untuk melihat apakah routing sudah benar, dapat dilihat dengan ```route -n```.

### D. DHCP Server-Relay

Selanjutnya, memberikan IP pada subnet GRESIK dan SIDOARJO. Pertama, install dhcp server pada MOJOKERTO dengan cara ```apt-get install isc-dhcp-server```, kemudian lakukan konfigurasi pada ```nano /etc/dhcp/dhcpd.conf``` seperti dibawah ini :

![dhcpsrvr](https://user-images.githubusercontent.com/62512432/103286802-a780c400-4a13-11eb-8fa1-6db10c182648.png)

dan pada ```nano /etc/default/isc-dhcp-server``` ditambahkan ```eth0``` pada ```INTERFACES```

![dhcpsrvr2](https://user-images.githubusercontent.com/62512432/103287254-a2704480-4a14-11eb-8272-4123846c5a96.png)

lalu direstart dengan perintah ```service isc-dhcp-server restart```.

Kedua, pada KEDIRI install dhcp relay dengan cara ```apt-get install isc-dhcp-relay```, kemudian lakukan konfigurasi pada ```nano //etc/default/isc-dhcp-relay``` seperti dibawah ini :

![dhcpkdr](https://user-images.githubusercontent.com/62512432/103288048-984f4580-4a16-11eb-9735-b42c7a95e6d9.png)

sama dengan BATU, lakukan seperti di KEDIRI

![dhcpbt](https://user-images.githubusercontent.com/62512432/103288216-fed46380-4a16-11eb-8a08-602dda78cafe.png)

Lalu DHCP dinyatakan berhasil apabila IP pad GRESIK dan SIDOARJO dapat terlihat di ```ifconfig```.


### 1. SURABAYA bisa akses keluar tanpa MASQUERADE

Pada UML SURABAYA, dibuat file dengan nama ```one.sh``` yang berisi iptables yang diminta sesuai dengan soal, yaitu :

![iptblsby](https://user-images.githubusercontent.com/62512432/103288457-7d310580-4a17-11eb-8a69-3de6117e6439.png)

untunk testing dapat dilakukan ```ping google.com``` pada setiap UML.

### 2. Akses SSH di luar topologi (UML) akan di-DROP ketika mengakses server yang memiliki IP DMZ (lakukan setting di SURABAYA)

Pada UML SURABAYA, dibuat file dengan nama ```two.sh``` yang berisi iptables yang diminta :

![2sby](https://user-images.githubusercontent.com/62512432/103288724-21b34780-4a18-11eb-81b1-8f3be25d5c09.png)

Kemudian untuk testing, dilakukan pada Putty dengan mengetikkan ```nc [IP] 22```.

### 3. DHCP Server dan DNS Server maksimal menerima 3 koneksi ICMP secara bersamaan, selebihnya di-DROP (disetting pada masing-masing server)

Pada MALANG dan MOJOKERTO, dibuat file dengan nama ```three.sh``` yang berisi iptables yang diminta :

![3mlg](https://user-images.githubusercontent.com/62512432/103289110-faa94580-4a18-11eb-83af-c77a405130dc.png)

![3mojo](https://user-images.githubusercontent.com/62512432/103289128-05fc7100-4a19-11eb-8874-941eea3a7450.png)

lalu dicoba dengan ping IP MOJOKERTO di UML selain MALANG dan MOJOKERTO.

### 4. Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat

Pada MALANG, dibuat file dengan nama ```four.sh``` yang berisi iptables yang diminta :

![4mlg](https://user-images.githubusercontent.com/62512432/103289390-a3f03b80-4a19-11eb-99da-c3106795dc1e.png)

kemudian untuk testing dapat dilakukan dengan cara menjalankan perintah iptables di UML MALANG dan routing di UML SIDOARJO. Apabila dilakukan diluar jam yang ditetapkan, maka routing tidak berhasil. Sebaliknya apabila dilakukan pada jam yang ditentukan, maka routing akan berhasil.

### 5.  Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya

Pada MALANG, dibuat file dengan nama ```five.sh``` yang berisi iptables yang diminta :

![5mlg](https://user-images.githubusercontent.com/62512432/103289528-06e1d280-4a1a-11eb-9440-ce88dd67d960.png)

testing dapat dilakukan dengan cara yang sama seperti pada MALANG dan SIDOARJO.

### 6. Ketika mengakses DNS Server akan secara bergantian didistribusikan ke PROBOLINGGO dan MADIUN pada port 80 (setting iptables di SURABAYA)

### 7. Logging semua paket yang di-DROP
