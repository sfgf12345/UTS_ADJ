# Connect GNS3 to the Internet (local server)
1.	Untuk membuat topologi GNS3 baru, pilih sekelompok perangkat di Devices Toolbar dengan mengklik tombol Browse Routers

![image](https://user-images.githubusercontent.com/66856996/138505757-a59729da-1193-4e46-8f87-05db61a861ec.png)

2.	Router yang tersedia akan tergantung pada konfigurasi GNS3 Anda. Dalam contoh ini tersedia router lokal dan router VM GNS3.

 ![image](https://user-images.githubusercontent.com/66856996/138505802-c6565ab2-2065-4758-9947-faed065e7179.png)

3.	Seret dan lepas router lokal ke GNS3 Workspace. Sebuah instance dari node menjadi tersedia di Workspace:
 ![image](https://user-images.githubusercontent.com/66856996/138505815-d489e487-0e74-495a-994f-f692926afd48.png)

4.	Seret dan lepas router server lokal lainnya ke GNS3 Workspace:

 ![image](https://user-images.githubusercontent.com/66856996/138505841-3ef57d6e-618f-4acd-bc7a-04847a5451c3.png)

5.	Drag and drop a Cloud node to the Workspace, select a local server, and then click OK
 ![image](https://user-images.githubusercontent.com/66856996/138505849-93af03b6-0ae3-4010-a731-53ab7fe12b1c.png)

6.	Right click on the Cloud and then click Configure

 ![image](https://user-images.githubusercontent.com/66856996/138505868-55427b40-e3c6-4e15-b89a-62234a5ed7c7.png)

7.	Klik pada topologi router pertama untuk menampilkan antarmuka yang tersedia (ini tergantung perangkat)
 ![image](https://user-images.githubusercontent.com/66856996/138505878-f5495ad2-7721-4e5e-a0d0-1510602214dd.png)

8.	Klik antarmuka dan kemudian pilih cloud di topologi untuk menghubungkan antarmuka ke sana. Dalam contoh ini FastEthernet 0/0 pada R1 dipilih. Selanjutnya, klik pada node Cloud, untuk melihat daftar antarmuka yang tersedia
 ![image](https://user-images.githubusercontent.com/66856996/138505894-7c05289b-9167-4a8b-ab52-a02e13d306b4.png)

9.	Pilih antarmuka di Cloud untuk menyelesaikan koneksi. Dalam contoh ini, Ethernet di Cloud 1 dipilih:
 ![image](https://user-images.githubusercontent.com/66856996/138505900-4c139c55-b99e-44d4-a0f9-e963f5a9b17b.png)

10.	Add another link between R2 and R1

 ![image](https://user-images.githubusercontent.com/66856996/138505915-98f4d3c7-1988-4910-9ba9-f1d36111a105.png)

11.	Anda sekarang siap untuk menyalakan perangkat jaringan Anda. Klik tombol Start/Resume pada GNS3 Toolbar untuk memulai perangkat jaringan Anda. Kemudian Anda sekarang siap untuk mengonfigurasi perangkat Anda. Klik tombol Console connect to all devices pada Toolbar untuk membuka koneksi ke setiap perangkat di topologi
12.	Configure IP addresses:
R1# configure terminal
R1(config)# interface FastEthernet 0/0
R1(config-if)# ip address dhcp
R1(config-if)# no shutdown
R1(config-if)# end
R1#
13.	Hasil: Alamat IP dialokasikan ke router oleh server DHCP:
R1#
*Mar  1 00:03:14.831: %DHCP-6-ADDRESS_ASSIGN: Interface FastEthernet0/0 assigned DHCP address 192.168.1.29, mask 255.255.255.0, hostname R1

14.	Opsi 2: Konfigurasi manual Jika mengkonfigurasi alamat IP statis, konfigurasikan R1 dengan alamat IP di subnet yang sama dengan PC lokal Anda:
R1# configure terminal
R1(config)# interface FastEthernet 0/0
R1(config-if)# ip address 192.168.1.123 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
15.	Konfigurasikan gateway default:
R1(config)# ip route 0.0.0.0 0.0.0.0 192.168.1.249
R1(config)# end
16.	Ping gateway default router:
R1# ping 192.168.1.249

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.249, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/17/36 ms
R1#
17.	Pastikan router dikonfigurasi untuk menggunakan server DNS yang benar:
R1# configure terminal

R1(config)# ip domain-lookup
R1(config)# ip name-server 8.8.8.8
R1(config)# end
R1#
18.	Coba tes dengan mengetik ping www.google.com
R1# ping google.com


Translating "google.com"...domain server (8.8.8.8) [OK]

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 216.58.198.174, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 12/19/24 ms
R1#
Jika ping Anda tidak berhasil, pastikan Anda memiliki konektivitas ke gateway default Anda dan pastikan gateway default dikonfigurasi untuk NAT untuk menerjemahkan alamat yang dialokasikan ke router GNS3.

19.	Konfigurasikan pengalamatan IP pada jaringan GNS3 Internal:

 ![image](https://user-images.githubusercontent.com/66856996/138505966-edec8e1c-b69a-4b2a-a5e5-e12bee6c0c97.png)
![image](https://user-images.githubusercontent.com/66856996/138505973-4484afe2-c4cf-4baf-8360-d606a3f3dc61.png)

 
20.	Konfigurasikan OSPF pada R1 dan R2 dan iklankan rute default:
R1R1(config)# router ospf 1
R1(config-router)# network 10.0.0.0 0.255.255.255 area 0
R1(config-router)# default-information originate
R1(config-router)# end
R1#

R2R2(config)# router ospf 1
R2(config-router)# network 10.0.0.0 0.255.255.255 area 0
R2(config-router)# end
R2#

Result OSPF neighbor relationships are established:

R1*Mar  1 00:19:24.431: %OSPF-5-ADJCHG: Process 1, Nbr 10.1.1.2 on FastEthernet0/1 from LOADING to FULL, Loading Done
R1#

R2*Mar  1 00:19:24.467: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.1.123 on FastEthernet0/0 from LOADING to FULL, Loading Done
R2#
21.	Konfigurasi pengaturan DNS pada R2:

 ![image](https://user-images.githubusercontent.com/66856996/138505986-17f3e179-8ac0-4958-af2b-40a414332e7c.png)

22.	R2 tidak akan dapat melakukan ping ke perangkat Internet sampai Anda mengonfigurasi NAT di R1 (atau mengaktifkan perutean antara R1 dan gateway Internet Anda). Dalam contoh ini, gateway Internet tidak mendukung perutean, jadi NAT akan dikonfigurasi pada R1: R1# konfigurasi terminal

 ![image](https://user-images.githubusercontent.com/66856996/138505994-b7b877bc-22e2-4de5-93f6-4f43a4a3f19a.png)

23.	Uji konektivitas R2 ke Internet:

 ![image](https://user-images.githubusercontent.com/66856996/138506003-b175a915-cc22-4025-a07f-86c9d52ec1fe.png)

Hasil R2 mampu melakukan ping perangkat di Internet.


------------------------------------------------------------------------------------------------------------------

# The NAT node

Dimulai dengan GNS3 2.0, node NAT menjadi tersedia. Node ini memungkinkan Anda untuk menghubungkan topologi ke internet melalui NAT. Node Internet tidak digunakan lagi untuk node ini, dan node Cloud.
1.	Untuk menambahkan simpul NAT ke topologi, seret dan lepas ke ruang kerja. Anda akan diminta untuk menentukan jenis server yang ingin Anda gunakan, untuk menjalankan node NAT. Artikel ini akan menggunakan wadah buruh pelabuhan Webterm untuk menguji konektivitas internet, sehingga jenis server perlu disetel ke GNS3 VM, karena ini dilakukan pada workstation Win10:
  ![image](https://user-images.githubusercontent.com/66856996/138508456-f668cb8a-798d-4657-992c-cca5485b9526.png)

2.	Selanjutnya, web docker Webterm juga akan ditambahkan ke workspace:

 ![image](https://user-images.githubusercontent.com/66856996/138508469-faf86232-91d9-4779-b52e-133076ee74b9.png)

3.	Untuk memungkinkan lebih dari satu node topologi memiliki akses ke internet, perlu untuk menghubungkan switch atau router ke node NAT, dan kemudian menghubungkan node topologi ke perangkat lain.
Untuk kesederhanaan, sakelar ethernet bawaan akan terhubung ke node NAT, dan wadah Webterm akan terhubung ke sakelar:

 ![image](https://user-images.githubusercontent.com/66856996/138508482-f342b3d2-1488-4e9d-870a-3fffbe771e9b.png)

4.	Anda mengaktifkan DHCP atau secara manual mengkonfigurasi penetapan IP statis ke wadah buruh pelabuhan seperti Webterm, dengan mengklik kanan padanya saat dimatikan, lalu pilih "Edit konfigurasi", lalu untuk mengkonfigurasi wadah ini untuk menggunakan DHCP, Anda batalkan komentar pada dua baris yang ditunjukkan pada gambar di bawah ini, dan klik Simpan:
 ![image](https://user-images.githubusercontent.com/66856996/138508495-5d4c272d-c361-4a57-a666-449897b5f53c.png)

5.	(batalkan komentar berarti menghilangkan simbol '#' di depan baris tersebut. Simbol itu menyebabkan sistem tidak membaca baris tersebut, dan biasanya digunakan untuk menambahkan komentar ke kode, yang seharusnya tidak diproses).
Memulai wadah Webterm (node NAT akan secara otomatis berjalan sejak ditambahkan ke topologi) dan membuka konsolnya akan menghasilkan jendela VNC yang muncul. Klik simbol "Pulihkan" di sudut kanan atas Firefox, untuk mengeluarkannya dari tampilan layar penuh, lalu klik kiri pada latar belakang hitam dan memilih "Terminal" akan membuka jendela terminal, kemudian menggunakan perintah 'ifconfig' di terminal akan menunjukkan bahwa DHCP yang berjalan pada node NAT memberi wadah ini alamat 192.168.122.200 dari kumpulannya:

 ![image](https://user-images.githubusercontent.com/66856996/138508506-5657745a-7b4b-43c1-a7b7-c7bfa1050492.png)

6.	Kembali ke Firefox, masukkan URL di bilah alamat, untuk mengakses situs web:
 ![image](https://user-images.githubusercontent.com/66856996/138508509-43badd6f-b73f-43a5-8d24-36f61cc14dd1.png)

7.	 Anda tidak dibatasi hanya menggunakan penetapan alamat dinamis dengan node NAT. Anda juga dapat menetapkan pengalamatan IP secara statis, dan masih memiliki akses internet.

Hentikan wadah Webterm, klik kanan, dan pilih "Edit konfigurasi" lagi.

Kali ini, Anda akan mengomentari dua baris untuk DHCP, dan menghapus komentar pada baris di bagian Static IP dari file /etc/network/interfaces;

8.	Klik Simpan, mulai wadah, dan konsol kembali ke dalamnya. Membuka terminal dan menjalankan "ifconfig" akan menunjukkan bahwa wadah menggunakan alamat IP yang ditetapkan secara statis:

 ![image](https://user-images.githubusercontent.com/66856996/138508521-a2104ee3-4836-421e-a2d7-968594b2a678.png)

9.	Entering a URL in the Firefox address bar will open a website:

 ![image](https://user-images.githubusercontent.com/66856996/138508531-4683def3-f59d-4a77-9713-8ad4f5cd11dc.png)

