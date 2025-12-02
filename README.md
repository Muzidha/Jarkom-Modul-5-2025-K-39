# Jarkom-Modul-5-2025-K-39

Nama :
1. Muhammad Ziddan Habibi
2. Raihan Fahri Alghazali

## Topologi
---

<img width="1296" height="734" alt="image" src="https://github.com/user-attachments/assets/d42c8d51-ca6f-4b56-9279-b58bb6be66d4" />

---

## Pendahuluan
Modul ini membahas implementasi keamanan jaringan dan manajemen layanan pada topologi "Middle-Earth". Praktikum mencakup konfigurasi DNS Server Authoritative, manajemen Firewall menggunakan `iptables` (filter, NAT, time-based, connection limit), deteksi intrusi (port scan), serta isolasi jaringan.

---

<img width="1077" height="571" alt="Screenshot 2025-11-28 193247" src="https://github.com/user-attachments/assets/19c17b29-9596-4a91-a241-f966bd0c7285" />

---

<img width="985" height="687" alt="Screenshot 2025-11-28 210837" src="https://github.com/user-attachments/assets/7bdf0f46-ad98-4a76-aa0d-8ca9139d5aa7" />

---

## Konfigurasi Setiap Node

---

### 1. Ironhills

```
auto eth0
iface eth0 inet static
    address 10.83.1.238
    netmask 255.255.255.252
    gateway 10.30.1.237
```

### 2. Moria

```
auto lo
iface lo inet loopback

# eth0: Mengarah ke ATAS (Osgiliath)
# IP Kita: .233, Gateway (Osgiliath): .234
auto eth0
iface eth0 inet static
    address 10.83.1.233
    netmask 255.255.255.252
    gateway 10.83.1.234

# eth1: Mengarah ke BAWAH (IronHills)
auto eth1
iface eth1 inet static
    address 10.83.1.237
    netmask 255.255.255.252

# eth2: Mengarah ke BAWAH (Wilderland)
auto eth2
iface eth2 inet static
    address 10.83.1.241
    netmask 255.255.255.252
```

### 3. Wilderland

```
auto lo
iface lo inet loopback

# eth0: Uplink menuju MORIA
# Moria memakai 10.83.1.241, jadi kita memakai 10.83.1.242
auto eth0
iface eth0 inet static
    address 10.83.1.242
    netmask 255.255.255.252
    gateway 10.83.1.241

# eth1: Gateway untuk Client DURIN (50 Host /26)
# Network: 10.83.1.128, Gateway kita: 10.83.1.129
auto eth1
iface eth1 inet static
    address 10.83.1.129
    netmask 255.255.255.192

# eth2: Gateway untuk Client KHAMUL (5 Host /29)
# Network: 10.83.1.200, Gateway kita: 10.83.1.201
auto eth2
iface eth2 inet static
    address 10.83.1.201
    netmask 255.255.255.248
```

### 4. Durin
```
auto lo
iface lo inet loopback
# Ke Wilderland (Gateway .129)
auto eth0
iface eth0 inet static
    address 10.83.1.130
    netmask 255.255.255.192
    gateway 10.83.1.129
```

### 5. Khamul
```
auto lo
iface lo inet loopback
# Ke Wilderland (Gateway .201)
auto eth0
iface eth0 inet static
    address 10.83.1.202
    netmask 255.255.255.248
    gateway 10.83.1.201
```

### 6. Osgiliath
```
auto lo
iface lo inet loopback

# Internet (Valinor)
auto eth0
iface eth0 inet dhcp
    # Script Firewall (SNAT) dijalankan saat interface nyala
    up iptables -t nat -A POSTROUTING -o eth0 -s 10.83.0.0/16 -j SNAT --to-source 192.168.122.225

# Ke Rivendell
auto eth1
iface eth1 inet static
    address 10.83.1.229
    netmask 255.255.255.252

# Ke Moria
auto eth2
iface eth2 inet static
    address 10.83.1.234
    netmask 255.255.255.252

# Ke Minastir
auto eth3
iface eth3 inet static
    address 10.83.1.225   
    netmask 255.255.255.252
```

### 7. Rivendell
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 10.83.1.230
    netmask 255.255.255.252
    gateway 10.83.1.229

auto eth1
iface eth1 inet static
    address 10.83.1.193
    netmask 255.255.255.248
```

### 8. Vilya
```
auto lo
iface lo inet loopback
# Ke Rivendell (Gateway .193)
auto eth0
iface eth0 inet static
    address 10.83.1.194
    netmask 255.255.255.248
    gateway 10.83.1.193
```

### 9. Narya
```
auto lo
iface lo inet loopback
# Ke Rivendell (Gateway .193)
auto eth0
iface eth0 inet static
    address 10.83.1.195
    netmask 255.255.255.248
    gateway 10.83.1.193
```

### 10. Minastir
```
auto lo
iface lo inet loopback

# Ke Osgiliath (Uplink/Gateway)
auto eth0
iface eth0 inet static
    address 10.83.1.226
    netmask 255.255.255.252
    gateway 10.83.1.225

# Ke Client Elendil (LAN)
auto eth1
iface eth1 inet static
    address 10.83.0.1
    netmask 255.255.255.0

# Ke Pelargir
auto eth2
iface eth2 inet static
    address 10.83.1.217
    netmask 255.255.255.252
```

### 11. Elendil
```
auto lo
iface lo inet loopback
# Ke Minastir (Gateway .1)
auto eth0
iface eth0 inet static
    address 10.83.0.2
    netmask 255.255.255.0
    gateway 10.83.0.1
```

### 12. Isildur
```
auto lo
iface lo inet loopback
# Ke Minastir (Gateway .1)
auto eth0
iface eth0 inet static
    address 10.83.0.3
    netmask 255.255.255.0
    gateway 10.83.0.1
```

### 13. Pelargir
```
auto lo
iface lo inet loopback
# eth0: Uplink ke MINASTIR (Gateway .217)
# Subnet: 10.83.1.216/30
auto eth0
iface eth0 inet static
    address 10.83.1.218
    netmask 255.255.255.252
    gateway 10.83.1.217

# eth1: Downlink ke ANDUINBANKS
# Subnet: 10.83.1.208/30 (Pelargir .209, Anduin .210)
auto eth1
iface eth1 inet static
    address 10.83.1.209
    netmask 255.255.255.252

# eth2: Downlink ke PALANTIR
# Subnet: 10.83.1.212/30 (Pelargir .213, Palantir .214)
auto eth2
iface eth2 inet static
    address 10.83.1.213
    netmask 255.255.255.252
```

### 14. Palantir
```
auto lo
iface lo inet loopback
# Ke Pelargir (Gateway .213)
auto eth0
iface eth0 inet static
    address 10.83.1.214
    netmask 255.255.255.252
    gateway 10.83.1.213
```

### 15. Anduinbanks
```
auto lo
iface lo inet loopback
# eth0: Uplink ke PELARGIR (Gateway .209)
# Subnet: 10.83.1.208/30
auto eth0
iface eth0 inet static
    address 10.83.1.210
    netmask 255.255.255.252
    gateway 10.83.1.209

# eth1: Downlink ke GILGALAD/CIRDAN
# Subnet: 10.83.1.0/25 (Gateway .1)
auto eth1
iface eth1 inet static
    address 10.83.1.1
    netmask 255.255.255.128
```

### 16. Gilgalad
```
auto lo
iface lo inet loopback
# Ke AnduinBanks (Gateway .1)
auto eth0
iface eth0 inet static
    address 10.83.1.2
    netmask 255.255.255.128
    gateway 10.83.1.1
```

### 17. Cirdan
```
auto lo
iface lo inet loopback
# Ke AnduinBanks (Gateway .1)
auto eth0
iface eth0 inet static
    address 10.83.1.3
    netmask 255.255.255.128
    gateway 10.83.1.1
```
## 1. KOnfigurasi Vilya sebagai DHCP server & Anduinbanks, Rivendell dan minastir sebagai DHCP Relay

**1. Masuk ke Vilya**, install paket (pastikan konek internet/repo):

```bash
apt-get update
apt-get install isc-dhcp-server -y
```

**2. Konfigurasi Interface:**
Edit file `/etc/default/isc-dhcp-server`:

```bash
INTERFACESv4="eth0"
```

**3. Konfigurasi Subnet:**
Edit file `/etc/dhcp/dhcpd.conf`. Hapus isinya atau tambahkan di paling bawah.
*(Catatan: Kita arahkan DNS ke Narya 10.83.1.195)*.

```bash
# Konfigurasi Global
default-lease-time 600;
max-lease-time 7200;
option domain-name "aliansi.lan";
option domain-name-servers 10.83.1.195, 8.8.8.8;

# Subnet Server (Network Vilya sendiri - Wajib ada supaya service mau jalan)
subnet 10.83.1.192 netmask 255.255.255.248 {
}

# Subnet ELENDIL & ISILDUR (Minastir)
subnet 10.83.0.0 netmask 255.255.255.0 {
  range 10.83.0.10 10.83.0.100;
  option routers 10.83.0.1;
}

# Subnet GILGALAD & CIRDAN (AnduinBanks)
subnet 10.83.1.0 netmask 255.255.255.128 {
  range 10.83.1.10 10.83.1.50;
  option routers 10.83.1.1;
}

# Subnet DURIN (Wilderland)
subnet 10.83.1.128 netmask 255.255.255.192 {
  range 10.83.1.135 10.83.1.180;
  option routers 10.83.1.129;
}

# Subnet KHAMUL (Wilderland)
subnet 10.83.1.200 netmask 255.255.255.248 {
  range 10.83.1.203 10.83.1.206;
  option routers 10.83.1.201;
}
```

**4. Start Service:**

```bash
service isc-dhcp-server start
# atau
/etc/init.d/isc-dhcp-server start
```

-----

### BAGIAN 2: Konfigurasi DHCP RELAY

Agar permintaan DHCP dari client (Elendil, Durin, dll) bisa sampai ke Vilya, router perantara harus menjadi "Relay".

**Note Penting:** Di soal tertulis AnduinBanks, Rivendell, dan Minastir. Namun, **Wilderland** juga WAJIB jadi relay karena Durin & Khamul ada di bawahnya.

Lakukan langkah ini di **Minastir, AnduinBanks, Rivendell, dan Wilderland**:

**1. Install Relay:**

```bash
apt-get install isc-dhcp-relay -y
```

**2. Konfigurasi Relay:**
Biasanya saat instalasi akan muncul popup konfigurasi. Jika tidak, edit file `/etc/default/isc-dhcp-relay`.

Isikan data berikut:

  * **SERVERS:** `10.83.1.194` (IP Vilya)
  * **INTERFACES:** Masukkan semua interface yang ada di router tersebut (misal `"eth0 eth1 eth2"`).

Contoh file `/etc/default/isc-dhcp-relay`:

```bash
SERVERS="10.83.1.194"
INTERFACES="eth0 eth1 eth2"
OPTIONS=""
```

**3. Start Service:**

```bash
service isc-dhcp-relay start
```

*Sekarang, coba set client (Durin/Elendil) ke mode DHCP, mereka harusnya dapat IP.*

-----

### BAGIAN 3: NARYA sebagai DNS Server

Narya akan menerjemahkan `palantir.aliansi.lan` ke IP Palantir.

**IP Narya:** `10.83.1.195` (Pastikan sudah diset statis).

**1. Install Bind9:**

```bash
apt-get install bind9 -y
```

**2. Konfigurasi Forwarder (Opsi Internet):**
Edit `/etc/bind/named.conf.options`:

```bash
options {
        directory "/var/cache/bind";
        forwarders {
                8.8.8.8;
        };
        allow-query { any; };
        dnssec-validation no; # Matikan validasi jika error di lab
};
```

**3. Buat Zone Lokal:**
Edit `/etc/bind/named.conf.local`:

```bash
zone "aliansi.lan" {
    type master;
    file "/etc/bind/db.aliansi";
};
```

**4. Isi Database DNS:**
Copy file default dan edit: `cp /etc/bind/db.local /etc/bind/db.aliansi`
Edit `/etc/bind/db.aliansi`:

```text
;
; BIND data file for aliansi.lan
;
$TTL    604800
@       IN      SOA     aliansi.lan. root.aliansi.lan. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      narya.aliansi.lan.
@       IN      A       10.83.1.195
narya   IN      A       10.83.1.195
vilya   IN      A       10.83.1.194
palantir IN     A       10.83.1.214
ironhills IN    A       10.83.1.238
```

**5. Start Service:**

```bash
service bind9 start
```
---


<img width="621" height="139" alt="image" src="https://github.com/user-attachments/assets/3485443a-5223-4ccf-ac82-6da441f33e9d" />

-----

### BAGIAN 4: PALANTIR & IRONHILLS sebagai Web Server

Kita pakai Nginx (lebih ringan) atau Apache. Di sini saya contohkan Nginx.

Lakukan ini di **Palantir** dan **IronHills**:

**1. Install Nginx:**

```bash
apt-get install nginx -y
```

**2. Buat index.html Custom:**

**Di Palantir:**

```bash
echo "<h1>Welcome to Palantir</h1>" > /var/www/html/index.html
```

**Di IronHills:**

```bash
echo "<h1>Welcome to IronHills</h1>" > /var/www/html/index.html
```

**3. Start Service:**

```bash
service nginx start
```

-----

<img width="765" height="311" alt="image" src="https://github.com/user-attachments/assets/0ebee91c-b72e-43c8-b129-237d2d3f47bd" />

---

## BAGIAN 2.1 konfigurasi routing menggunakan iptables agar jaringan aliansi bisa terhubung ke luar (Valinor/Internet)

```
iptables -t nat -A POSTROUTING -o eth0 -s 10.83.0.0/16 -j SNAT --to-source 192.168.122.225

# Hapus IP yang mungkin salah/nempel
ip addr flush dev eth3

# Pasang IP yang benar (Pasangan dari Minastir .226)
ip addr add 10.83.1.225/30 dev eth3
ip link set eth3 up

# Rute ke Elendil via Minastir
ip route add 10.83.0.0/24 via 10.83.1.226

# Agar Osgiliath bisa ping ke Elendil & Pelargir (lewat Minastir)
ip route add 10.83.0.0/24 via 10.83.1.226
ip route add 10.83.1.216/30 via 10.83.1.226

# Agar Osgiliath bisa ping ke Server Vilya/Narya (lewat Rivendell)
ip route add 10.83.1.192/29 via 10.83.1.230

# Agar Osgiliath bisa ping ke IronHills & Wilderland (lewat Moria)
ip route add 10.83.1.236/30 via 10.83.1.233
ip route add 10.83.1.240/30 via 10.83.1.233

# Rute ke IronHills (Subnet .236) via Moria (.233)
ip route add 10.83.1.236/30 via 10.83.1.233

# Rute ke Wilderland (Subnet .240) via Moria (.233)
ip route add 10.83.1.240/30 via 10.83.1.233

# Beri tahu Osgiliath: "Semua jaringan Pelargir & Palantir titip ke Minastir (.>
# Kita rangkum subnet Palantir, Pelargir, Anduin jadi satu blok /28 biar praktis
ip route add 10.83.1.208/28 via 10.83.1.226

# Beri tahu Osgiliath: "Jaringan Gilgalad/Cirdan (.0/25) titip ke Minastir (.22>
ip route add 10.83.1.0/25 via 10.83.1.226

```

**Ujung Kanan**

<img width="965" height="333" alt="image" src="https://github.com/user-attachments/assets/445f18a4-193f-4575-8849-c05e6508ab9b" />

**Ujung Kiri**

<img width="957" height="334" alt="image" src="https://github.com/user-attachments/assets/3bcd22d8-4f88-497f-802a-1e938ac9a6d8" />

## BAGIAN 2.2 Keamanan Vilya (ICMP Protection)

Tugas ini meminta kita menerapkan aturan Firewall (iptables) yang spesifik: **Blokir akses masuk (INPUT) khusus protokol ICMP tipe "Request", tapi izinkan yang lain.**

Prinsip kerjanya:

1.  **Ping** terdiri dari dua bagian: **Bertanya (Echo Request)** dan **Menjawab (Echo Reply)**.
2.  Agar orang lain tidak bisa Ping Vilya: Kita blokir **"Pertanyaan"** yang masuk ke Vilya.
3.  Agar Vilya tetap bisa Ping orang lain: Kita biarkan Vilya mengirim pertanyaan keluar, dan kita biarkan Vilya menerima **"Jawaban"** (Reply) yang masuk.

-----

### Konfigurasi di VILYA

Masuk ke terminal node **Vilya**, lalu jalankan satu baris perintah sakti ini:

```bash
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
```

### Penjelasan Perintah:

  * `-A INPUT`: Aturan ini berlaku untuk paket yang **masuk** ke Vilya.
  * `-p icmp`: Protokol yang diatur adalah **ICMP** (Ping).
  * `--icmp-type echo-request`: Hanya targetkan tipe **Request** (orang yang coba "mencolek" Vilya).
  * `-j DROP`: Buang paketnya diam-diam (jangan dijawab).

-----

**ping ke vilya**

<img width="790" height="160" alt="image" src="https://github.com/user-attachments/assets/6082ff8a-9344-421a-a04f-d90f3bbbc7d3" />

ping dari vilya

<img width="816" height="411" alt="image" src="https://github.com/user-attachments/assets/9a31798d-8fcf-49ea-b0cd-0d72efb05a8b" />

3. Pembatasan Akses DNS (Port 53)
Deskripsi: Untuk menjaga kerahasiaan lokasi pasukan, akses ke port DNS (53) pada Narya dibatasi. Hanya IP Vilya yang diizinkan mengakses, sedangkan IP lain diblokir.

Konfigurasi (Node Narya): Menggunakan prinsip Whitelisting: Izinkan Vilya terlebih dahulu, lalu Drop semua sisanya.

Bash
```

# Whitelist Vilya
iptables -A INPUT -s 10.83.1.194 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -s 10.83.1.194 -p tcp --dport 53 -j ACCEPT

# Blacklist Others
iptables -A INPUT -p udp --dport 53 -j DROP
iptables -A INPUT -p tcp --dport 53 -j DROP
Bukti: Vilya sukses mengakses DNS (Validasi menggunakan nc):
```

Client lain gagal mengakses DNS:

vilya dapat mengakses dns narya

<img width="755" height="75" alt="image" src="https://github.com/user-attachments/assets/81e8cd4f-1402-48fa-8bfc-5840e94c5396" />

narya memblokir semua yang mengakses dns selain vilya

<img width="929" height="453" alt="image" src="https://github.com/user-attachments/assets/ab16c066-3765-4376-81ac-d4b04af3388d" />


berhasil akses ironhills ketika hari weekend
<img width="942" height="569" alt="image" src="https://github.com/user-attachments/assets/d75b65b8-0425-4ca8-9c50-4970a4565208" />

ironhills ketika weekday
<img width="665" height="137" alt="image" src="https://github.com/user-attachments/assets/5ae96ea1-c571-4e7b-ac96-2cfbe471718c" />
tidak bisa diakses, tanda nya koneksi lama

ubah date di palantir
<img width="417" height="66" alt="Screenshot 2025-12-01 081806" src="https://github.com/user-attachments/assets/b59104cf-12cd-4853-b32d-b2581137fe82" />

gilgalad bisa mengakses palantir
<img width="758" height="117" alt="Screenshot 2025-12-01 081759" src="https://github.com/user-attachments/assets/971abc1e-02e5-40aa-92af-4c55f2ca4d6e" />

palantir tidak bisa mengakses
<img width="917" height="190" alt="image" src="https://github.com/user-attachments/assets/04e33c9b-18da-41c5-ad00-122f8bbe84fe" />

malam hari elendil succes mengakses palantir
<img width="954" height="524" alt="image" src="https://github.com/user-attachments/assets/960d66bb-f50a-41e1-a61a-67b22a1598a9" />

malam hari gilgalad gagal mengakses palantir
<img width="877" height="68" alt="image" src="https://github.com/user-attachments/assets/5c3ca823-a024-4a91-8a7d-80433f816229" />

setelah nge-scan elendil kena blacklist 20 detik
<img width="905" height="430" alt="image" src="https://github.com/user-attachments/assets/eb242cc9-edad-4021-97d6-b2b1c16d788a" />

daftar blacklist di palantir
<img width="1889" height="110" alt="image" src="https://github.com/user-attachments/assets/0145c9f3-225e-46ae-80a3-b725be0d4d3d" />

log hasil nmap, catatan di palantir
<img width="1077" height="215" alt="image" src="https://github.com/user-attachments/assets/4cb238f1-31fa-4aaa-a9b0-3f02c0522505" />

menolak ke palantir dari elendil
<img width="1028" height="180" alt="image" src="https://github.com/user-attachments/assets/d428631a-729b-497a-8526-ee246bb5a07b" />

man in the middle sukses
<img width="1800" height="116" alt="image" src="https://github.com/user-attachments/assets/b8431a77-aeb5-4986-a190-abff884acf2c" />

khamul terisolasi namun durin masih bisa di akses
<img width="1892" height="1056" alt="image" src="https://github.com/user-attachments/assets/13d7f8a4-f15b-47c2-ac01-284390a19130" />
