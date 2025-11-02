# Jarkom-Modul-3-2025-K21

Member :
1. Danuja Prasasta Bastu (5027241037)
2. Naila Cahyarani Idelia (5027241063)

<div align=center>

# Soal Modul 3

</div>

- [Question 1 & 2](#question-1--2)
- [Question 3](#question-3)

## Topologi

<img width="1585" height="820" alt="image" src="https://github.com/user-attachments/assets/e92391cc-3440-41a9-999a-109d58d82c8a" />

## Question 1 & 2

> Di awal Zaman Kedua, setelah kehancuran Beleriand, para Valar menugaskan untuk membangun kembali jaringan komunikasi antar kerajaan. Para Valar menyalakan Minastir, Aldarion, Erendis, Amdir, Palantir, Narvi, Elros, Pharazon, Elendil, Isildur, Anarion, Galadriel, Celeborn, Oropher, Miriel, Amandil, Gilgalad, Celebrimbor, Khamul, dan pastikan setiap node (selain Durin sang penghubung antar dunia) dapat sementara berkomunikasi dengan Valinor/Internet (nameserver 192.168.122.1) untuk menerima instruksi awal.

Lakuin Instalasi DHCP Server (Aldarion) dengan jalankan perintah berikut:

```bash
apt-get update
apt-get install isc-dhcp-server
dhcpd --version
```

Selanjutnya konfigurasi Interface DHCP Server. Edit file konfigurasi default:

```bash
nano /etc/default/isc-dhcp-server

#Tambahkan 
INTERFACESv4="eth0"
```

Lalu konfigurasi dhcpd.conf. Edit file utama konfigurasi DHCP:

```bash
nano /etc/dhcp/dhcpd.conf

#Masukkan konfigurasi berikut sesuai pembagian subnet dalam soal:
# Subnet untuk Keluarga Manusia (N  menor)
subnet 10.74.1.0 netmask 255.255.255.0 {
    range 10.74.1.6 10.74.1.34;
    range 10.74.1.68 10.74.1.94;
    option routers 10.74.1.1;
    option broadcast-address 10.74.1.255;
    option domain-name-servers 10.74.3.3, 10.74.3.2; # DNS Master dan Slave
    default-lease-time 600;
    max-lease-time 7200;
}

# Subnet untuk Keluarga Peri (Elf)
subnet 10.74.2.0 netmask 255.255.255.0 {
    range 10.74.2.35 10.74.2.67;
    range 10.74.2.96 10.74.2.121;
    option routers 10.74.2.1;
    option broadcast-address 10.74.2.255;
    option domain-name-servers 10.74.3.3, 10.74.3.2;
    default-lease-time 600;
    max-lease-time 7200;
}

# Subnet untuk jaringan Kurcaci (Relay)
subnet 10.74.3.0 netmask 255.255.255.0 {
    option routers 10.74.3.1;
    option broadcast-address 10.74.3.255;
    option domain-name-servers 10.74.4.2;
}

# Subnet untuk server & database
subnet 10.74.4.0 netmask 255.255.255.0 {
    option routers 10.74.4.1;
    option broadcast-address 10.74.4.255;
    option domain-name-servers 10.74.3.3, 10.74.3.2;
}

# Fixed address untuk Khamul
host Khamul {
hardware ethernet 02:42:a2:bd:ad:00;
fixed-address 10.74.3.95;
}
```

Setelah konfigurasi selesai kita restart DHCP server, jalankan:

```bash
service isc-dhcp-server restart
```

<img width="1919" height="87" alt="image" src="https://github.com/user-attachments/assets/ee75d142-0c38-4af2-8780-52b1c557d8cb" />

Lakuin Instalasi DHCP Relay (Durin) dengan jalankan perintah berikut:

```bash
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

Edit konfigurasi DHCP relay:

```bash
nano /etc/default/isc-dhcp-relay

SERVERS=”10.74.4.4”
INTERFACES=”eth1 eth2 eth3 eth4 eth5”
OPTIONS=””
```

Selanjutnya mengaktifkan IP Forwarding agar paket dari subnet lain dapat diteruskan oleh Durin:

```bash
nano /etc/sysctl.conf

net.ipv4.ip_forward=1
```

Lalu aktifkan:

```bash
sysctl -p
```

Restart DHCP Relay:

```bash
service isc-dhcp-relay restart
```

<img width="1034" height="211" alt="image" src="https://github.com/user-attachments/assets/4ad41f1a-30ec-4db9-a664-6e5b31f6ddd6" />

Untuk semua client dinamis (misal Khamul, Gilgalad, Amandil) ubah konfigurasi interface menjadi:

```bash
auto eth0
iface eth0 inet dhcp
```

Cek IP hasil lease DHCP pakai `ip a` misal di Khamul

<img width="1919" height="910" alt="image" src="https://github.com/user-attachments/assets/ffa9e664-6596-4ea1-a894-833d5e413776" />

Langkah selanjutnya test connectivity

1. Test dari beberapa node:
```bash
# Test dari Khamul
ping 192.168.122.1
```

2. Test DNS resolution:
```bash
# Test dari client mana saja
ping google.com -c 5
```
Jika DNS resolution bermasalah lakukan dibawah ini
```bash
nano /etc/resolv.conf

#Tambahkan
nameserver 8.8.8.8
nameserver 1.1.1.1
```

3. Verifikasi routing:
```bash
# Cek routing table
route -n

# Cek default gateway
ip route show
```

<img width="1906" height="869" alt="Screenshot 2025-11-01 132555" src="https://github.com/user-attachments/assets/5359c7a4-978d-4741-a394-db3b42a4d1fe" />

## Question 3

> Untuk mengontrol arus informasi ke dunia luar (Valinor/Internet), sebuah menara pengawas, Minastir didirikan. Minastir mengatur agar semua node (kecuali Durin) hanya dapat mengirim pesan ke luar Arda setelah melewati pemeriksaan di Minastir.

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```
