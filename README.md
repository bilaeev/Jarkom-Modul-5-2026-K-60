# Jarkom-Modul-5-2026-K-60
# Modul 5 - Kelompok K-60

| Item | Nilai |
|---|---|
| Kelompok | K-60 (Group C) |
| Blok IP kelompok | `192.241.56.0/21` |
| Jumlah router | 17 (Gendou sebagai router pusat, terhubung ke NAT1 lewat `eth0`) |
| Image Docker | `ardhptr21/alpinet` (router), `ardhptr21/debinet` (host) |
| Script | `/root/<nama-node>.sh` di tiap node, salinannya ada di folder `scripts/` |

Bagian ini mendokumentasikan soal 1-10. Soal 11-20 dikerjakan partner kelompok.

---

## Soal 1 - VLSM

### Penjelasan

Soal ini membagi blok IP kelompok menjadi 17 subnet LAN dan 16 subnet link antar-router. Asisten tidak memberikan network address khusus, sehingga blok induk dipilih sendiri. Total kebutuhan sekitar 1.200 alamat, sedangkan `/22` hanya menyediakan 1.022 alamat usable. Karena itu dipakai `/21` (2.046 usable) dengan network `192.241.56.0/21`, yang mencakup `192.241.56.0` sampai `192.241.63.255`.

Aturan yang dipakai:

- Kebutuhan alamat sebuah LAN = jumlah host + 1 (IP gateway).
- Subnet dialokasikan dari yang terbesar ke terkecil. Jika ukurannya sama, LAN didahulukan sebelum link router, lalu mengikuti urutan tabel soal.
- Gateway adalah IP usable pertama pada subnet. Container pada switch memakai IP usable berikutnya.
- Satu container mewakili satu grup host. Jumlah host pada soal hanya dipakai untuk menentukan ukuran subnet.
- Setiap link antar-router memakai `/30` (2 IP usable).

Contoh perhitungan Quarters: 500 host + 1 gateway = 501 alamat. Karena 2^8 = 256 belum cukup dan 2^9 = 512 sudah cukup, dibutuhkan 9 bit host, sehingga prefix = 32 - 9 = `/23` (510 usable).

Dikerjakan di spreadsheet dan gambar, bukan di terminal. File: `01_VLSM/soal01_tabel_vlsm.xlsx`.

### Tabel pembagian IP - LAN

| No | LAN | Host | Kebutuhan | Prefix | Network | Gateway | Broadcast | Router gateway |
|---|---|---|---|---|---|---|---|---|
| 1 | Quarters | 500 | 501 | /23 | 192.241.56.0 | 192.241.56.1 | 192.241.57.255 | Rei |
| 2 | Comm | 200 | 201 | /24 | 192.241.58.0 | 192.241.58.1 | 192.241.58.255 | Hikari |
| 3 | Hangar | 100 | 101 | /25 | 192.241.59.0 | 192.241.59.1 | 192.241.59.127 | Yui |
| 4 | Bunker | 55 | 56 | /26 | 192.241.59.128 | 192.241.59.129 | 192.241.59.191 | Kensuke |
| 5 | Medic | 26 | 27 | /27 | 192.241.59.192 | 192.241.59.193 | 192.241.59.223 | Maya |
| 6 | Relay | 25 | 26 | /27 | 192.241.59.224 | 192.241.59.225 | 192.241.59.255 | Kaworu |
| 7 | Garden | 14 | 15 | /27 | 192.241.60.0 | 192.241.60.1 | 192.241.60.31 | Shinji |
| 8 | Dock | 12 | 13 | /28 | 192.241.60.32 | 192.241.60.33 | 192.241.60.47 | Mari |
| 9 | Lab | 5 | 6 | /29 | 192.241.60.48 | 192.241.60.49 | 192.241.60.55 | Ritsuko |
| 10 | Terminal | 3 | 4 | /29 | 192.241.60.56 | 192.241.60.57 | 192.241.60.63 | Fuyutsuki |
| 11 | Cage | 3 | 4 | /29 | 192.241.60.64 | 192.241.60.65 | 192.241.60.71 | Misato |
| 12 | Dogma | 2 | 3 | /29 | 192.241.60.72 | 192.241.60.73 | 192.241.60.79 | Toji |
| 13 | Monolith | 2 | 3 | /29 | 192.241.60.80 | 192.241.60.81 | 192.241.60.87 | Shigeru |
| 14 | Radar | 4 | 5 | /29 | 192.241.60.88 | 192.241.60.89 | 192.241.60.95 | Makoto |
| 15 | Archive | 1 | 2 | /30 | 192.241.60.96 | 192.241.60.97 | 192.241.60.99 | Asuka |
| 16 | Intel | 1 | 2 | /30 | 192.241.60.100 | 192.241.60.101 | 192.241.60.103 | Kaji |
| 17 | Cmd | 1 | 2 | /30 | 192.241.60.104 | 192.241.60.105 | 192.241.60.107 | Gendou |

### Tabel pembagian IP - link antar-router

Semua link memakai `/30` (mask `255.255.255.252`). IP pertama dipakai router yang disebut lebih dulu.

| No | Link | Network | IP router pertama | IP router kedua |
|---|---|---|---|---|
| 1 | Gendou - Fuyutsuki | 192.241.60.108/30 | 192.241.60.109 | 192.241.60.110 |
| 2 | Gendou - Shigeru | 192.241.60.112/30 | 192.241.60.113 | 192.241.60.114 |
| 3 | Gendou - Asuka | 192.241.60.116/30 | 192.241.60.117 | 192.241.60.118 |
| 4 | Gendou - Hikari | 192.241.60.120/30 | 192.241.60.121 | 192.241.60.122 |
| 5 | Gendou - Yui | 192.241.60.124/30 | 192.241.60.125 | 192.241.60.126 |
| 6 | Gendou - Kaji | 192.241.60.128/30 | 192.241.60.129 | 192.241.60.130 |
| 7 | Gendou - Misato | 192.241.60.132/30 | 192.241.60.133 | 192.241.60.134 |
| 8 | Fuyutsuki - Maya | 192.241.60.136/30 | 192.241.60.137 | 192.241.60.138 |
| 9 | Fuyutsuki - Toji | 192.241.60.140/30 | 192.241.60.141 | 192.241.60.142 |
| 10 | Shigeru - Makoto | 192.241.60.144/30 | 192.241.60.145 | 192.241.60.146 |
| 11 | Asuka - Ritsuko | 192.241.60.148/30 | 192.241.60.149 | 192.241.60.150 |
| 12 | Asuka - Kensuke | 192.241.60.152/30 | 192.241.60.153 | 192.241.60.154 |
| 13 | Hikari - Kaworu | 192.241.60.156/30 | 192.241.60.157 | 192.241.60.158 |
| 14 | Yui - Rei | 192.241.60.160/30 | 192.241.60.161 | 192.241.60.162 |
| 15 | Yui - Mari | 192.241.60.164/30 | 192.241.60.165 | 192.241.60.166 |
| 16 | Yui - Shinji | 192.241.60.168/30 | 192.241.60.169 | 192.241.60.170 |

### Tabel IP container

| Container | LAN | IP | Gateway |
|---|---|---|---|
| Quarters | Quarters | 192.241.56.2/23 | 192.241.56.1 |
| Comm-Net | Comm | 192.241.58.2/24 | 192.241.58.1 |
| Hangar-Crew | Hangar | 192.241.59.2/25 | 192.241.59.1 |
| Sachiel | Bunker | 192.241.59.130/26 | 192.241.59.129 |
| Pen-Pen | Medic | 192.241.59.194/27 | 192.241.59.193 |
| Medic-Bay | Medic | 192.241.59.195/27 | 192.241.59.193 |
| Relay-Node | Relay | 192.241.59.226/27 | 192.241.59.225 |
| Garden-Staff | Garden | 192.241.60.2/27 | 192.241.60.1 |
| Dock-Crew | Dock | 192.241.60.34/28 | 192.241.60.33 |
| Entry-Plug | Lab | 192.241.60.50/29 | 192.241.60.49 |
| Dummy-Plug | Lab | 192.241.60.51/29 | 192.241.60.49 |
| Magi-Casper | Terminal | 192.241.60.58/29 | 192.241.60.57 |
| Magi-Balthasar | Terminal | 192.241.60.59/29 | 192.241.60.57 |
| Magi-Melchior | Terminal | 192.241.60.60/29 | 192.241.60.57 |
| Unit-01 | Cage | 192.241.60.66/29 | 192.241.60.65 |
| Unit-02 | Cage | 192.241.60.67/29 | 192.241.60.65 |
| Unit-00 | Cage | 192.241.60.68/29 | 192.241.60.65 |
| Lilith | Dogma | 192.241.60.74/29 | 192.241.60.73 |
| Adam | Dogma | 192.241.60.75/29 | 192.241.60.73 |
| Zeruel | Monolith | 192.241.60.82/29 | 192.241.60.81 |
| Armisael | Monolith | 192.241.60.83/29 | 192.241.60.81 |
| Radar-Array | Radar | 192.241.60.90/29 | 192.241.60.89 |
| Archive-Vault | Archive | 192.241.60.98/30 | 192.241.60.97 |
| Spy | Intel | 192.241.60.102/30 | 192.241.60.101 |
| Tokyo3-Core | Cmd | 192.241.60.106/30 | 192.241.60.105 |

### Pohon IP

Garis dan kotak berwarna merah menandai jalur Dummy-Plug ke Armisael: Switch-Lab, Ritsuko, Asuka, Gendou, Shigeru, Switch-Monolith, Armisael. Subnet yang dilewati: Lab `192.241.60.48/29`, link Asuka-Ritsuko `192.241.60.148/30`, link Gendou-Asuka `192.241.60.116/30`, link Gendou-Shigeru `192.241.60.112/30`, dan Monolith `192.241.60.80/29`.

![Pohon IP K-60](01_VLSM/soal01_pohon_ip.png)

---

## Soal 2 - Topologi, IP interface, dan IP forwarding

### Penjelasan

Topologi dibangun di GNS3 sesuai diagram soal, memakai Docker `alpinet` untuk router dan `debinet` untuk host. Setiap router diberi IP pada tiap interface sesuai tabel VLSM, lalu IP forwarding diaktifkan. Tanpa `net.ipv4.ip_forward=1`, sebuah router hanya menjawab paket yang ditujukan kepadanya dan tidak meneruskan paket antar-interface, sehingga tidak berfungsi sebagai router.

Pemetaan interface ditentukan dari label kabel di GNS3. Pada Gendou: `eth0` ke NAT1 (DHCP), `eth1` Switch-Cmd, `eth2` Fuyutsuki, `eth3` Shigeru, `eth4` Misato, `eth5` Kaji, `eth6` Asuka, `eth7` Hikari, `eth8` Yui.

Dikerjakan di console masing-masing router. Script disimpan di `/root/<router>.sh` (salinan: `scripts/routers/`). Pola setiap router:

```sh
ip link set eth0 up; ip addr replace 192.241.60.138/30 dev eth0   # contoh: Maya ke Fuyutsuki
ip link set eth1 up; ip addr replace 192.241.59.193/27 dev eth1   # contoh: gateway LAN Medic
sysctl -w net.ipv4.ip_forward=1
```

Pembuktian: `ip -br addr` pada tiap router dan ping ke kedua ujung tiap link.

### Output

Gendou:

```
Gendou:~# ip -br addr
eth0   UNKNOWN  192.168.122.100/24
eth1   UNKNOWN  192.241.60.105/30
eth2   UNKNOWN  192.241.60.109/30
eth3   UNKNOWN  192.241.60.113/30
eth4   UNKNOWN  192.241.60.133/30
eth5   UNKNOWN  192.241.60.129/30
eth6   UNKNOWN  192.241.60.117/30
eth7   UNKNOWN  192.241.60.121/30
eth8   UNKNOWN  192.241.60.125/30
Gendou:~# ping -c 3 192.168.122.1
3 packets transmitted, 3 received, 0% packet loss
```

Router lain (hanya interface yang dikonfigurasi, disertai ping ke tetangga di link yang sama):

```
Maya:    eth0 192.241.60.138/30  eth1 192.241.59.193/27   | ping 192.241.60.137 -> 0% loss
Toji:    eth0 192.241.60.142/30  eth1 192.241.60.73/29    | ping 192.241.60.141 -> 0% loss
Shigeru: eth0 192.241.60.114/30  eth1 192.241.60.81/29  eth2 192.241.60.145/30 | ping 192.241.60.113 -> 0% loss
Makoto:  eth0 192.241.60.146/30  eth1 192.241.60.89/29    | ping 192.241.60.145 -> 0% loss
Asuka:   eth0 192.241.60.118/30  eth1 192.241.60.153/30  eth2 192.241.60.149/30  eth3 192.241.60.97/30 | ping 192.241.60.117 -> 0% loss
Ritsuko: eth0 192.241.60.150/30  eth1 192.241.60.49/29   | ping 192.241.60.149 -> 0% loss
Kensuke: eth0 192.241.60.154/30  eth1 192.241.59.129/26  | ping 192.241.60.153 -> 0% loss
Hikari:  eth0 192.241.60.122/30  eth1 192.241.60.157/30  eth2 192.241.58.1/24 | ping 192.241.60.121 -> 0% loss
Kaworu:  eth0 192.241.60.158/30  eth1 192.241.59.225/27  | ping 192.241.60.157 -> 0% loss
Yui:     eth0 192.241.60.126/30  eth1 192.241.60.169/30  eth2 192.241.60.161/30  eth3 192.241.60.165/30  eth4 192.241.59.1/25 | ping 192.241.60.125 -> 0% loss
Rei:     eth0 192.241.60.162/30  eth1 192.241.56.1/23     | ping 192.241.60.161 -> 0% loss
Mari:    eth0 192.241.60.166/30  eth1 192.241.60.33/28    | ping 192.241.60.165 -> 0% loss
Shinji:  eth0 192.241.60.170/30  eth1 192.241.60.1/27     | ping 192.241.60.169 -> 0% loss
Kaji:    eth0 192.241.60.130/30  eth1 192.241.60.101/30   | ping 192.241.60.129 -> 0% loss
Misato:  eth0 192.241.60.134/30  eth1 192.241.60.65/29    | ping 192.241.60.133 -> 0% loss
Fuyutsuki: eth0 192.241.60.110/30  eth1 192.241.60.57/29  eth2 192.241.60.137/30  eth3 192.241.60.141/30
```

Ping dari sisi parent ke anak (ujung link yang satunya), semuanya `0% packet loss`:

```
Gendou    -> 192.241.60.110 .114 .118 .122 .126 .130 .134
Fuyutsuki -> 192.241.60.138 .142
Shigeru   -> 192.241.60.146
Asuka     -> 192.241.60.150 .154
Hikari    -> 192.241.60.158
Yui       -> 192.241.60.162 .166 .170
```

Semua router: `net.ipv4.ip_forward = 1`.

---

## Soal 3 - Static routing

### Penjelasan

Setiap router perlu tahu ke mana mengirim paket untuk subnet yang tidak terhubung langsung. Karena topologinya berbentuk pohon dengan Gendou di tengah, aturannya sederhana:

- Router di ujung (hanya satu link ke atas) cukup memakai default route ke router di atasnya.
- Router perantara (Fuyutsuki, Shigeru, Asuka, Hikari, Yui) memakai default route ke Gendou ditambah rute spesifik ke subnet yang berada di bawahnya.
- Gendou memakai default route dari DHCP NAT1 (`192.168.122.1`) untuk internet, dan satu rute statis untuk setiap subnet yang berada di balik router lain.

Rute yang berurutan dan bisa digabung tepat menjadi satu blok CIDR diringkas (route summarization) agar tabel lebih kecil:

- `192.241.60.136/29` = link Fuyutsuki-Maya (`.136/30`) + link Fuyutsuki-Toji (`.140/30`)
- `192.241.60.80/28` = Monolith (`.80/29`) + Radar (`.88/29`)
- `192.241.60.160/29` = link Yui-Rei (`.160/30`) + link Yui-Mari (`.164/30`)

Dikerjakan di console tiap router, ditambahkan ke `/root/<router>.sh` dengan penanda `# ===== SOAL 3 =====`. Contoh pola:

```sh
# Ritsuko (router ujung): hanya default route ke Asuka
ip route replace default via 192.241.60.149

# Fuyutsuki (router perantara): default ke Gendou + rute ke bawah
ip route replace default via 192.241.60.109
ip route replace 192.241.59.192/27 via 192.241.60.138   # LAN Medic lewat Maya
ip route replace 192.241.60.72/29 via 192.241.60.142    # LAN Dogma lewat Toji
```

### Output

`ip route` pada Gendou:

```
default via 192.168.122.1 dev eth0 metric 99194
192.241.56.0/23 via 192.241.60.126 dev eth8
192.241.58.0/24 via 192.241.60.122 dev eth7
192.241.59.0/25 via 192.241.60.126 dev eth8
192.241.59.128/26 via 192.241.60.118 dev eth6
192.241.59.192/27 via 192.241.60.110 dev eth2
192.241.59.224/27 via 192.241.60.122 dev eth7
192.241.60.0/27 via 192.241.60.126 dev eth8
192.241.60.32/28 via 192.241.60.126 dev eth8
192.241.60.48/29 via 192.241.60.118 dev eth6
192.241.60.56/29 via 192.241.60.110 dev eth2
192.241.60.64/29 via 192.241.60.134 dev eth4
192.241.60.72/29 via 192.241.60.110 dev eth2
192.241.60.80/28 via 192.241.60.114 dev eth3
192.241.60.96/30 via 192.241.60.118 dev eth6
192.241.60.100/30 via 192.241.60.130 dev eth5
192.241.60.136/29 via 192.241.60.110 dev eth2
192.241.60.144/30 via 192.241.60.114 dev eth3
192.241.60.148/30 via 192.241.60.118 dev eth6
192.241.60.152/30 via 192.241.60.118 dev eth6
192.241.60.156/30 via 192.241.60.122 dev eth7
192.241.60.160/29 via 192.241.60.126 dev eth8
192.241.60.168/30 via 192.241.60.126 dev eth8
```

Pengujian dari Armisael (host sementara diberi IP final):

```
root@Armisael:~# ping -c 3 192.241.60.74    # Lilith    -> 0% packet loss, ttl=60
root@Armisael:~# ping -c 3 192.241.59.130   # Sachiel   -> 0% packet loss, ttl=60
root@Armisael:~# ping -c 3 192.241.58.2     # Comm-Net  -> 0% packet loss, ttl=61
```

TTL 60 dari Armisael ke Lilith menunjukkan paket melewati 4 router (Shigeru, Gendou, Fuyutsuki, Toji).

---

## Soal 4 - NAT dan akses internet

### Penjelasan

Seluruh alamat internal (`192.241.56.0/21`) tidak dikenal di internet, sehingga paket yang keluar lewat NAT1 harus diubah alamat sumbernya. Hal ini dilakukan hanya di Gendou karena hanya Gendou yang terhubung ke NAT1 (`eth0`).

Pilihan yang dipakai:

- Target `MASQUERADE`, bukan `SNAT --to-source <IP>`. `MASQUERADE` membaca IP interface keluar saat paket dikirim, sehingga tetap bekerja walaupun IP DHCP dari NAT1 berubah. `SNAT` memerlukan IP tetap dan harus diedit setiap IP berganti.
- Aturan dibatasi `-s 192.241.56.0/21 -o eth0`, sehingga hanya trafik dari prefix internal yang keluar lewat NAT1 yang diterjemahkan. Trafik antar-LAN tidak melewati `eth0` sehingga alamat aslinya tidak berubah.
- Perintah `-C` (cek) dijalankan sebelum `-A` (tambah) agar script yang dijalankan ulang tidak membuat aturan ganda.

Host yang perlu me-resolve nama domain diberi `nameserver 8.8.8.8` di `/etc/resolv.conf`.

Dikerjakan di console Gendou, disimpan di `/root/gendo.sh` dengan penanda `# ===== SOAL 4 =====`:

```sh
command -v iptables >/dev/null 2>&1 || apk add --no-cache iptables
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -C POSTROUTING -s 192.241.56.0/21 -o eth0 -j MASQUERADE 2>/dev/null || iptables -t nat -A POSTROUTING -s 192.241.56.0/21 -o eth0 -j MASQUERADE
```

### Output

```
Gendou:~# iptables -t nat -S
-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
-A POSTROUTING -s 192.241.56.0/21 -o eth0 -j MASQUERADE

Gendou:~# ping -c 2 8.8.8.8
2 packets transmitted, 2 received, 0% packet loss

Gendou:~# iptables -t nat -L POSTROUTING -n -v
 pkts bytes target     prot opt in  out   source           destination
    8   604 MASQUERADE all  --  *   eth0  192.241.56.0/21  0.0.0.0/0
```

Dari Armisael dan Lilith (setelah `echo "nameserver 8.8.8.8" > /etc/resolv.conf`):

```
ping -c 3 8.8.8.8          -> 0% packet loss
ping -c 3 deb.debian.org   -> 0% packet loss
```

Counter `pkts` pada aturan MASQUERADE bertambah (bukan nol), tanda aturan benar-benar mengenai paket.

---

## Soal 5 - Firewall Gendou

### Penjelasan

Firewall dipasang di Gendou dengan prinsip default deny: policy chain `INPUT` dan `FORWARD` diubah menjadi `DROP`, lalu hanya trafik yang diizinkan yang dibuka.

| Kebutuhan | Aturan |
|---|---|
| Balasan koneksi yang sudah ada | `conntrack --ctstate ESTABLISHED,RELATED` di `INPUT` dan `FORWARD` |
| Internal ke internet | `FORWARD`: sumber `192.241.56.0/21`, keluar lewat `eth0` |
| Antar-LAN | `FORWARD`: sumber dan tujuan di `192.241.56.0/21`, bukan masuk dari `eth0` |
| Internal ke Gendou sendiri | `INPUT`: sumber `192.241.56.0/21`, bukan dari `eth0` |
| Loopback | `INPUT -i lo` |
| DHCP dari NAT1 | `INPUT`: UDP port sumber 67 ke tujuan 68 pada `eth0` |

Koneksi baru yang datang dari sisi NAT1 tidak cocok dengan aturan mana pun, sehingga jatuh ke policy `DROP`. Opsi `! -i eth0` mencegah paket dari sisi NAT1 yang memalsukan sumber internal. Policy `DROP` dipasang paling akhir agar sesi console tidak terputus saat aturan masih disusun. Script hanya mengosongkan chain `INPUT` dan `FORWARD`, tabel `nat` (soal 4) tidak disentuh.

Dikerjakan di console Gendou, disimpan di `/root/gendo.sh` dengan penanda `# ===== SOAL 5 =====`:

```sh
INT=192.241.56.0/21
WAN=eth0
iptables -F INPUT
iptables -F FORWARD
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i $WAN -p udp --sport 67 --dport 68 -j ACCEPT
iptables -A INPUT -s $INT ! -i $WAN -j ACCEPT
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -s $INT -o $WAN -j ACCEPT
iptables -A FORWARD -s $INT -d $INT ! -i $WAN -j ACCEPT
iptables -P INPUT DROP
iptables -P FORWARD DROP
```

### Output

```
Gendou:~# iptables -S
-P INPUT DROP
-P FORWARD DROP
-P OUTPUT ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i eth0 -p udp -m udp --sport 67 --dport 68 -j ACCEPT
-A INPUT -s 192.241.56.0/21 ! -i eth0 -j ACCEPT
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.241.56.0/21 -o eth0 -j ACCEPT
-A FORWARD -s 192.241.56.0/21 -d 192.241.56.0/21 ! -i eth0 -j ACCEPT
```

Soal 2 sampai 4 masih berjalan setelah firewall dipasang:

```
root@Armisael:~# ping -c 3 8.8.8.8          -> 0% packet loss
root@Armisael:~# ping -c 3 192.241.60.74    -> 0% packet loss
Gendou:~# ping -c 2 192.241.60.110          -> 0% packet loss
Gendou:~# ping -c 2 8.8.8.8                 -> 0% packet loss
```

Counter dari `iptables -L -n -v`: `INPUT` ESTABLISHED 4 paket, `FORWARD` ESTABLISHED 10 paket, `FORWARD` internet 1 paket, `FORWARD` antar-LAN 1 paket.

---

## Soal 6 - IP statis permanen untuk semua host

### Penjelasan

Setiap node non-router (24 container) diberi IP statis dan default gateway yang tetap ada setelah node di-restart. Konfigurasi `ip addr` biasa hanya hidup di memori, sehingga hilang saat node dimatikan. Agar permanen, konfigurasi ditulis ke `/etc/network/interfaces` (dipertahankan oleh GNS3 untuk node Docker), dan script disimpan di `/root/<host>.sh`. Baris `up echo "nameserver 8.8.8.8" > /etc/resolv.conf` ikut ditulis agar DNS tersedia setelah restart.

Daftar IP, prefix, dan gateway tiap host ada di tabel IP container pada soal 1. Dikerjakan di console masing-masing host. Contoh Unit-01:

```sh
mkdir -p /etc/network
printf 'auto lo\niface lo inet loopback\nauto eth0\niface eth0 inet static\n  address 192.241.60.66\n  netmask 255.255.255.248\n  gateway 192.241.60.65\n  up echo "nameserver 8.8.8.8" > /etc/resolv.conf\n' > /etc/network/interfaces
ip link set eth0 up
ip addr replace 192.241.60.66/29 dev eth0
ip route replace default via 192.241.60.65
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

Pembuktian permanen dilakukan di Unit-01: Stop lalu Start node di GNS3, kemudian IP, rute, dan script diperiksa tanpa menjalankan script lagi.

### Output

Setelah Stop dan Start Unit-01:

```
root@Unit-01:~# ip -br addr
eth0   UNKNOWN  192.241.60.66/29
root@Unit-01:~# ip route
default via 192.241.60.65 dev eth0
192.241.60.64/29 dev eth0 proto kernel scope link src 192.241.60.66
root@Unit-01:~# ls /root
unit-01.sh
root@Unit-01:~# ping -c 3 192.241.60.65
3 packets transmitted, 3 received, 0% packet loss
```

Pengecekan seluruh 24 host dari Lilith:

```
192.241.56.2 OK    192.241.58.2 OK    192.241.59.2 OK    192.241.59.130 OK
192.241.59.194 OK  192.241.59.195 OK  192.241.59.226 OK  192.241.60.2 OK
192.241.60.34 OK   192.241.60.50 OK   192.241.60.51 OK   192.241.60.58 OK
192.241.60.59 OK   192.241.60.60 OK   192.241.60.66 OK   192.241.60.67 OK
192.241.60.68 OK   192.241.60.75 OK   192.241.60.82 OK   192.241.60.83 OK
192.241.60.90 OK   192.241.60.98 OK   192.241.60.102 OK  192.241.60.106 OK
```

---

## Soal 7 - Membangunkan MAGI

### Penjelasan

Tiga node MAGI (Casper, Balthasar, Melchior) diminta menerima pesan teks lewat TCP. Langkahnya:

- Memasang `iptables` dan `netcat-traditional` lewat script di `/root/<nama>.sh`.
- Menjalankan listener `nc.traditional -l -p <port>` di port yang ditentukan: Casper 5000, Balthasar 5000 dan 5001, Melchior 5000.
- Listener dibungkus `while true` karena `nc.traditional -l` berhenti setelah satu koneksi selesai. Dengan loop, listener hidup kembali dan bisa menerima pesan berikutnya.
- Pesan yang masuk ditambahkan ke `/root/nc-<port>.log` agar bisa ditampilkan.
- Fungsi `start_listener` memeriksa dulu apakah port sudah `LISTEN`, jadi script aman dijalankan ulang tanpa membuat listener ganda.

Pengamanan belum dipasang pada soal ini sesuai instruksi. Dikerjakan di console masing-masing node MAGI, contoh Balthasar:

```sh
apt-get update -qq
DEBIAN_FRONTEND=noninteractive apt-get install -y -qq iptables netcat-traditional
start_listener() {
  ss -ltn | grep -q ":$1 " && return
  nohup sh -c "while true; do nc.traditional -l -p $1 >> /root/nc-$1.log; done" >/dev/null 2>&1 &
}
start_listener 5000
start_listener 5001
```

Pengiriman pesan dilakukan dari Lilith:

```sh
echo "Halo Casper dari Lilith" | nc -w 2 192.241.60.58 5000
echo "Halo Balthasar 5000 dari Lilith" | nc -w 2 192.241.60.59 5000
echo "Halo Balthasar 5001 dari Lilith" | nc -w 2 192.241.60.59 5001
echo "Halo Melchior dari Lilith" | nc -w 2 192.241.60.60 5000
```

### Output

Port yang `LISTEN` (`ss -ltn`):

```
Magi-Casper:    0.0.0.0:5000
Magi-Balthasar: 0.0.0.0:5000  dan  0.0.0.0:5001
Magi-Melchior:  0.0.0.0:5000
```

Pesan yang diterima:

```
root@Magi-Casper:~# cat /root/nc-5000.log
Halo Casper dari Lilith

root@Magi-Melchior:~# cat /root/nc-5000.log
Halo Melchior dari Lilith

root@Magi-Balthasar:~# cat /root/nc-5000.log /root/nc-5001.log
Halo Balthasar 5000 dari Lilith
Halo Balthasar 5001 dari Lilith
```

---

## Soal 8 - Layanan Pen-Pen dan Tokyo3-Core

### Penjelasan

Pen-Pen dan Tokyo3-Core diminta menjalankan layanan sederhana pada kondisi normal:

- Port 80 di Pen-Pen dan Tokyo3-Core membalas `Welcome to {hostname}`.
- Port 8080 di Tokyo3-Core membalas `syifa imutz`.
- Port lain yang tidak dilayani (misalnya 22) harus gagal dengan cepat, bukan menggantung.

Polanya mirip soal 7, tetapi setiap koneksi dijawab satu baris lalu ditutup (`nc.traditional -q 1`). Nama host diambil otomatis dengan `$(hostname)`. Karena tidak ada firewall di kedua host, port yang tidak punya listener langsung dibalas RST oleh kernel dan klien menerima `Connection refused`. Waktu respons yang sangat singkat membuktikan penolakan terjadi seketika, bukan timeout.

Dikerjakan di console masing-masing node, contoh Tokyo3-Core:

```sh
apt-get update -qq
DEBIAN_FRONTEND=noninteractive apt-get install -y -qq iptables netcat-traditional
serve() {
  ss -ltn | grep -q ":$1 " && return
  nohup sh -c "while true; do echo \"$2\" | nc.traditional -l -p $1 -q 1; done" >/dev/null 2>&1 &
}
serve 80 "Welcome to $(hostname)"
serve 8080 "syifa imutz"
```

### Output

```
Pen-Pen:     LISTEN 0.0.0.0:80
Tokyo3-Core: LISTEN 0.0.0.0:80  dan  0.0.0.0:8080
```

Pengujian dari Lilith:

```
root@Lilith:~# nc -w 3 192.241.59.194 80
Welcome to Pen-Pen          (exit=0)
root@Lilith:~# nc -w 3 192.241.60.106 80
Welcome to Tokyo3-Core      (exit=0)
root@Lilith:~# nc -w 3 192.241.60.106 8080
syifa imutz                 (exit=0)

root@Lilith:~# time nc -w 5 -vz 192.241.59.194 22
nc: connect to 192.241.59.194 port 22 (tcp) failed: Connection refused
real    0m0.002s
root@Lilith:~# time nc -w 5 -vz 192.241.60.106 22
nc: connect to 192.241.60.106 port 22 (tcp) failed: Connection refused
real    0m0.002s
```

---

## Soal 9 - Perbaikan routing Shigeru

### Penjelasan

Script `break_routing.sh` dari asisten dijalankan di Shigeru tanpa dibaca isinya. Penyebab kerusakan dicari sendiri dengan membandingkan kondisi sebelum dan sesudah script dijalankan:

```sh
snap() { { echo "## addr"; ip -br addr; echo "## link"; ip link; echo "## route"; ip route; echo "## rule"; ip rule; echo "## neigh"; ip neigh; echo "## iptables"; iptables-save 2>/dev/null; echo "## sysctl"; sysctl -a 2>/dev/null | grep -E 'ip_forward|rp_filter|accept_redirects|send_redirects|proxy_arp|icmp_echo'; } > "$1"; }
snap /root/before.txt
/root/break_routing.sh
snap /root/after.txt
diff -u /root/before.txt /root/after.txt
```

Gejala: Lilith tidak bisa ping Armisael, tetapi Armisael masih bisa ping gateway-nya (`192.241.60.81`). Sebelum dirusak, ping Lilith ke Armisael `0% packet loss`.

Hasil `diff -u` dan isi tabel routing 100:

```
-default via 192.241.60.113 dev eth0
+default via 192.241.60.146 dev eth2
+blackhole 192.241.4.72/29
+90:    from all iif eth1 lookup 100
-net.ipv4.conf.all.accept_redirects = 0
+net.ipv4.conf.all.accept_redirects = 1
-net.ipv4.ip_forward = 1
+net.ipv4.ip_forward = 0

Shigeru:~# ip route show table 100
unreachable default
```

Penjelasan tiap penyebab:

| Perubahan oleh script | Dampak |
|---|---|
| `ip_forward` menjadi 0 | Shigeru berhenti meneruskan paket antar-interface, sehingga ping Lilith ke Armisael (yang harus lewat Shigeru) mati. Ping Armisael ke `192.241.60.81` tetap hidup karena tujuannya Shigeru sendiri dan tidak perlu diteruskan |
| Default route berpindah ke Makoto (`192.241.60.146`) | paket tujuan di luar zona dikirim ke bawah. Makoto mengembalikannya ke Shigeru, sehingga paket berputar sampai TTL habis |
| `ip rule` prioritas 90 `iif eth1 lookup 100` dengan tabel 100 berisi `unreachable default` | semua paket yang masuk dari sisi Monolith (Armisael dan Zeruel) dicocokkan ke tabel yang isinya "tidak ada jalan", lalu dibuang |
| `blackhole 192.241.4.72/29` | subnet ini di luar blok K-60 sehingga tidak memengaruhi gejala, tetap dihapus agar kondisi kembali ke baseline |
| `accept_redirects` menjadi 1 | router menerima ICMP redirect sehingga rutenya bisa dibelokkan pihak lain, dikembalikan ke 0 |

Perbaikan disimpan di `/root/fix_routing.sh` pada Shigeru:

```sh
#!/bin/sh
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv4.conf.all.accept_redirects=0
ip route replace default via 192.241.60.113 dev eth0
ip route del blackhole 192.241.4.72/29 2>/dev/null
ip rule del pref 90 2>/dev/null
ip route flush table 100 2>/dev/null
```

### Output

Setelah perbaikan, snapshot baru dibandingkan dengan `before.txt` dan hasilnya kosong, artinya kondisi Shigeru sama persis seperti sebelum dirusak:

```
Shigeru:~# snap /root/fixed.txt
Shigeru:~# diff -u /root/before.txt /root/fixed.txt
(tidak ada output)

Shigeru:~# ip route
default via 192.241.60.113 dev eth0
192.241.60.80/29 dev eth1 proto kernel scope link src 192.241.60.81
192.241.60.88/29 via 192.241.60.146 dev eth2
192.241.60.112/30 dev eth0 proto kernel scope link src 192.241.60.114
192.241.60.144/30 dev eth2 proto kernel scope link src 192.241.60.145
```

Pengujian konektivitas:

```
root@Lilith:~# ping -c 3 -W 1 192.241.60.83     -> 0% packet loss
root@Armisael:~# ping -c 3 -W 1 192.241.60.74   -> 0% packet loss
root@Armisael:~# ping -c 3 -W 1 8.8.8.8         -> 0% packet loss
```

---

## Soal 10 - Perbaikan NAT Cage

### Penjelasan

Script `break_nat.sh` dari asisten dijalankan satu kali di Misato dan satu kali di Gendou, tanpa dibaca isinya. Metodenya sama seperti soal 9: snapshot sebelum dan sesudah, lalu `diff -u`. Fungsi `snap` yang dipakai sama dengan soal 9, dengan tambahan `ip route show table all` agar tabel routing lain ikut tercatat.

Gejala: Unit-01, Unit-02, dan Unit-00 di Cage tidak bisa ping 8.8.8.8, sedangkan Cage ke Lilith normal dan Spy di Intel tetap normal.

```
root@Unit-01:~# ping -c 3 -W 1 8.8.8.8          -> 100% packet loss
root@Unit-01:~# ping -c 3 -W 1 192.241.60.74    -> 0% packet loss
root@Spy:~#     ping -c 3 -W 1 8.8.8.8          -> 0% packet loss

Misato:~# diff -u /root/before.txt /root/after.txt
+-A POSTROUTING -s 192.241.60.64/29 ! -d 192.241.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1

Gendou:~# diff -u /root/before.txt /root/after.txt      (script mencetak "Subnet tidak terdeteksi")
+-A POSTROUTING -s 192.241.4.64/29 -o eth0 -j RETURN
```

Penjelasan:

- **Misato**: aturan `SNAT` mengubah sumber paket Cage (`192.241.60.64/29`) menjadi `172.31.99.1` untuk semua tujuan di luar `192.241.0.0/16`. Alamat `172.31.99.1` tidak ada di rencana IP. Di Gendou, aturan `MASQUERADE` hanya cocok untuk sumber `192.241.56.0/21`, jadi paket bersumber `172.31.99.1` tidak diterjemahkan lagi, keluar ke NAT1 apa adanya, dan balasannya tidak kembali.
- **Cage ke Lilith tetap normal**: tujuannya `192.241.x.x`, yang dikecualikan oleh `! -d 192.241.0.0/16`, sehingga alamat sumber asli tidak berubah.
- **Intel (Spy) tidak terdampak**: jalurnya melewati Kaji, bukan Misato, dan aturan `SNAT` hanya ada di Misato untuk subnet Cage.
- **Gendou**: aturan `RETURN` berarti "jangan NAT subnet ini" dan diselipkan sebelum `MASQUERADE`. Subnet `192.241.4.64/29` bukan bagian dari blok K-60 sehingga aturan ini tidak memengaruhi trafik kita. Aturan tetap dihapus agar kondisi kembali ke baseline.
- **Alamat sumber diterjemahkan di Gendou** (lewat `MASQUERADE`), bukan di Misato.

Perbaikan disimpan di `/root/fix_nat.sh` dan dijalankan di Misato dan Gendou. Perintah `-C` di dalam loop membuat script aman dijalankan berulang tanpa error atau duplikat:

```sh
#!/bin/sh
while iptables -t nat -C POSTROUTING -s 192.241.60.64/29 ! -d 192.241.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1 2>/dev/null; do
  iptables -t nat -D POSTROUTING -s 192.241.60.64/29 ! -d 192.241.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1
done
while iptables -t nat -C POSTROUTING -s 192.241.4.64/29 -o eth0 -j RETURN 2>/dev/null; do
  iptables -t nat -D POSTROUTING -s 192.241.4.64/29 -o eth0 -j RETURN
done
```

### Output

Setelah perbaikan:

```
Misato:~# iptables -t nat -S
-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
(tidak ada aturan SNAT)

Gendou:~# diff -u /root/before.txt /root/fixed.txt
(hanya timestamp dan counter yang berbeda, aturan NAT tetap hanya MASQUERADE)

root@Unit-01:~# ping -c 3 -W 1 8.8.8.8          -> 0% packet loss
root@Unit-02:~# ping -c 5 -W 1 8.8.8.8          -> 0% packet loss
```

Counter `MASQUERADE` di Gendou naik setelah Unit-02 melakukan ping, membuktikan translasi sumber terjadi di Gendou:

```
Gendou:~# iptables -t nat -L POSTROUTING -n -v      (sebelum, lalu setelah ping Unit-02)
 pkts bytes target     prot opt in  out   source           destination
   56  4010 MASQUERADE all  --  *   eth0  192.241.56.0/21  0.0.0.0/0
   58  4178 MASQUERADE all  --  *   eth0  192.241.56.0/21  0.0.0.0/0
```
