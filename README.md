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

Jika DNS resolution bermasalah lakukan dibawah ini
```bash
nano /etc/resolv.conf

#Tambahkan
nameserver 8.8.8.8
nameserver 1.1.1.1

#Atau
echo "nameserver 192.168.122.1" > /etc/resolv.conf
```

```bash
# Test dari client mana saja
ping google.com -c 5
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

Lakuin Instalasi package BIND9 (Minastir) dengan jalankan perintah berikut:
```bash
apt-get update && apt-get install bind9 -y
```

Edit konfigurasi DNS reslover Minastir
```bash
nano /etc/bind/named.conf.options

options {
    directory "/var/cache/bind";
    listen-on { any; };
    listen-on-v6 { any; };
    allow-query { any; };

    // Forward ke DNS external (Valinor/Internet)
    forwarders {
        192.168.122.1;
        8.8.8.8;
    };

    // Optional: batasi recursive DNS hanya untuk internal
    // allow-recursion { 10.74.0.0/16; };

    dnssec-validation auto;
    auth-nxdomain no;
};
```

Lalu, mengubah konfigurasi DHCP agar semua klien menggunakan DNS Minastir. Pada Aldarion (DHCP Server) edit:
```bash
nano /etc/dhcp/dhcpd.conf

# Update setiap subnet DNS diarahkan ke Minastir
# Subnet Keluarga Manusia
subnet 10.74.1.0 netmask 255.255.255.0 {
    range 10.74.1.6 10.74.1.34;
    range 10.74.1.68 10.74.1.94;
    option routers 10.74.1.1;
    option broadcast-address 10.74.1.255;
    option domain-name-servers 10.74.5.2;
    default-lease-time 600;
    max-lease-time 7200;
}

# Subnet Keluarga Peri
subnet 10.74.2.0 netmask 255.255.255.0 {
    range 10.74.2.35 10.74.2.67;
    range 10.74.2.96 10.74.2.121;
    option routers 10.74.2.1;
    option broadcast-address 10.74.2.255;
    option domain-name-servers 10.74.5.2;
    default-lease-time 600;
    max-lease-time 7200;
}

# Subnet Kurcaci
subnet 10.74.3.0 netmask 255.255.255.0 {
    option routers 10.74.3.1;
    option broadcast-address 10.74.3.255;
    option domain-name-servers 10.74.5.2;
}

# Subnet Server & Database
subnet 10.74.4.0 netmask 255.255.255.0 {
    option routers 10.74.4.1;
    option broadcast-address 10.74.4.255;
    option domain-name-servers 10.74.5.2;
}

host Khamul {
    hardware ethernet 02:42:a2:bd:ad:00;
    fixed-address 10.74.3.95;
}
```

Restart DHCP
```bash
service isc-dhcp-server restart
```

Restart service
```bash
service named restart
```

Setelah itu kita verivikasi pada klien lain. Pastikan klien DHCP mendapat DNS baru
```bash
cat /etc/resolv.conf
```

Coba ping di Khamul
```bash
ping google.com -c 3
```

## Question 4

> Ratu Erendis, sang pembuat peta, menetapkan nama resmi untuk wilayah utama (<xxxx>.com). Ia menunjuk dirinya (ns1.<xxxx>.com) dan muridnya Amdir (ns2.<xxxx>.com) sebagai penjaga peta resmi. Setiap lokasi penting (Palantir, Elros, Pharazon, Elendil, Isildur, Anarion, Galadriel, Celeborn, Oropher) diberikan nama domain unik yang menunjuk ke lokasi fisik tanah mereka. Pastikan Amdir selalu menyalin peta (master-slave) dari Erendis dengan setia.

Konfigurasi DNS Master (Erendis)
Lakuin Instalasi package BIND9 dengan jalankan perintah berikut:
```bash
apt-get update && apt-get install bind9 -y
```

Konfigurasi forwarders & transfer di master
```bash
nano /etc/bind/named.conf.options

options {
    directory "/var/cache/bind";
    listen-on { any; };
    listen-on-v6 { any; };
    allow-query { any; };
    allow-transfer { 10.74.3.3; }; // Amdir (slave)

    forwarders {
        192.168.122.1;
        8.8.8.8;
    };

    dnssec-validation auto;
    auth-nxdomain no;
};
```

Tambahkan zona master
```bash
nano /etc/bind/named.conf.local

zone "k21.com" {
    type master;
    file "/etc/bind/zones/k21.com.db";
    allow-transfer { 10.74.3.3; };
};

zone "1.74.10.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/rev.1.74.10.in-addr.arpa";
};
```

Buat direktori zone
```bash
mkdir /etc/bind/zones
```

File zona forward lookup (k21.com.db)
```bash
nano /etc/bind/zones/k21.com.db

$TTL 604800
@   IN  SOA ns1.k21.com. root.k21.com. (
        2024110101 ; Serial
        604800     ; Refresh
        86400      ; Retry
        2419200    ; Expire
        604800 )   ; Negative Cache TTL

; Name Servers
@       IN  NS  ns1.k21.com.
@       IN  NS  ns2.k21.com.

; A Records
@       IN  A       10.74.1.2
ns1     IN  A       10.74.3.2   ; Erendis (master)
ns2     IN  A       10.74.3.3   ; Amdir (slave)

; Lokasi penting
palantir    IN A 10.74.1.10
elros       IN A 10.74.1.11
pharazon    IN A 10.74.1.12
elendil     IN A 10.74.1.13
isildur     IN A 10.74.1.14
anarion     IN A 10.74.1.15
galadriel   IN A 10.74.2.20
celeborn    IN A 10.74.2.21
oropher     IN A 10.74.2.22

; CNAME
www     IN CNAME k21.com.

2.3     IN      PTR     ns1.k21.com.
3.3     IN      PTR     ns2.k21.com.
10.1    IN      PTR     palantir.k21.com.
11.1    IN      PTR     elros.k21.com.
```

Restart dan check
```bash
service named restart

named-checkconf

named-checkzone k21.com /etc/bind/zones/k21.com.db

service named status
```

Konfigurasi DNS Slave (Amdir)
Lakuin Instalasi package BIND9 dengan jalankan perintah berikut:
```bash
apt-get update && apt-get install bind9 -y
```

Konfigurasi forwarder (Slave)
```bash
nano /etc/bind/named.conf.options

options {
    directory "/var/cache/bind";
    listen-on { any; };
    listen-on-v6 { any; };
    allow-query { any; };

    forwarders {
        192.168.122.1;
        8.8.8.8;
    };

    dnssec-validation auto;
    auth-nxdomain no;
};
```

Daftarkan zona slave
```bash
nano /etc/bind/named.conf.local

zone "k21.com" {
    type slave;
    file "/var/cache/bind/k21.com.db";
    masters { 10.74.3.2; }; // Master: Erendis
};

zone "1.74.10.in-addr.arpa" {
    type slave;
    file "/var/cache/bind/rev.1.74.10.in-addr.arpa";
    masters { 10.74.3.2; };
};
```

Restart
```bash
service named restart
```
Coba di client lain (contoh Khamul)
```bash
nslookup k21.com
```
