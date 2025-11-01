# Jarkom-Modul-3-2025-K21

Member :
1. Danuja Prasasta Bastu (5027241037)
2. Naila Cahyarani Idelia (5027241063)

<div align=center>

# Soal Modul 2

</div>

- [Question 1](#question-1)
- [Question 2](#question-2)
- [Question 3](#question-3)

## Topologi



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
INTERFACES="eth1 eth2 eth3 eth4 eth5
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

Untuk semua client dinamis (misal Khamul, Gilgalad, Amandil) ubah konfigurasi interface menjadi:

```bash
auto eth0
iface eth0 inet dhcp
```

Cek IP hasil lease DHCP pakai `ip a` misal di Khamul

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
