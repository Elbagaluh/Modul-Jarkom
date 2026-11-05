# Modul 3 Jaringan Komputer

## Daftar Isi

- [1. Static Routing](#1-static-routing)
  - [1.1 Pengertian](#11-pengertian)
  - [1.2 Implementasi](#12-implementasi)
  - [1.3 Troubleshooting](#13-troubleshooting)
  - [1.4 Referensi](#14-referensi)

## 1. Static Routing

Setelah kita memahami bagaimana cara jaringan komputer di dunia bekerja pada umumnya, perangkat-perangkat yang digunakan untuk mendukung terjadinya komunikasi di internet, protokol yang dipatuhi, dan lain-lainnya, maka kita akan masuk ke dalam konsep yang tak kalah penting dan akan sering kita temui, yaitu **Routing**.

### 1.1 Pengertian

Jika IP Address bisa kita ibaratkan sebuah alamat rumah, maka sekarang mari kita bayangkan kita sedang berada di suatu perumahan, katakanlah perumahan A. Di perumahan ini, penduduknya mengikuti aturan alamat ruamah yang sama, yaitu huruf A diikuti oleh angka. Misal, tetanggamu yang bernama Bapak Hassan memiliki alamat rumah A-17, Ibu Rumrowi memiliki alamat rumah A-32, dan seterusnya. Sebagai penduduk setempat, kamu kenal dengan mereka dan tahu bagaimana cara mengunjungi rumah mereka tanpa bantuan orang lain.

Suatu hari, kamu diberi tugas untuk mengirimkan makanan ke alamat rumah B-03. Namun, kamu tidak kenal alamat ini karena tidak diawali oleh huruf A seperti biasanya. Maka dari itu, kamu menghubungi kantor pos perumahan dengan alamat A-01 (yang mana kamu tahu letaknya) dan meminta bantuan mereka untuk mengirimkan makanan tersebut. Sebagai kantor pos, mereka memiliki data alamat di seluruh kota, sehingga mereka tahu di mana letak rumah B-01 ini dan ke mana harus memberikan makanan tersebut. Jadi, kamu cukup pergi ke A-01 dan menyerahkan makanan tersebut yang memiliki tujuan B-01. Sisanya akan diurus oleh kantor pos.

Konsep routing kurang lebih sama seperti ilustrasi di atas. Pada hakikatnya, routing merupakan suatu proses yang menentukan jalur terbaik untuk mengirimkan suatu paket/data dari satu jaringan ke jaringan lainnya. Umumnya, hal ini dilakukan oleh _Router_, dimana mereka bertanggung jawab untuk meneruskan packet dari satu segmen jaringan ke segmen lainnya. _Router_ melakukan proses penentuan jalur ini berdasarkan data yang mereka punya, yaitu _Routing Table_ yang berisikan informasi mengenai cara untuk bisa menuju suatu segmen jaringan, sekalipun itu terlihat jauh.

Bila mengacu pada ilustrasi di atas, kamu dan seluruh tetanggamu bisa dianggap sebagai host dan perumahanmu dianggap sebagai suatu _subnet_ (Kita akan mendalami ini di Modul 4). Kemudian, kantor pos bisa dianggap sebagai _Router_ dan data alamat di seluruh kota yang mereka miliki bisa dianggap sebagai _Routing Table_.

![image](../Modul-2/images/routing_id.png)

Berdasarkan bagaimana cara _Router_ memperoleh informasi terkait _Routing Table_-nya, _Routing_ bisa dibagi menjadi 2 kategori, yaitu **Static Routing** dan **Dynamic Routing**. Untuk Dynamic Routing akan dibahas secara sekilas pada Modul 4, sedangkan Static Routing akan kita pelajari pada modul ini.

Pada Static Routing, seorang (atau tim) _network administrator_ bertanggung jawab untuk mengisi _Routing Table_ pada setiap _Router_. Jika menggunakan analogi kantor pos, pemerintah bertanggung jawab untuk memberi tahu seluruh kantor pos yang ada mengenai kantor pos lainnya dan bagaimana cara suatu kantor pos menghubungi kantor pos lain ataupun perumahan lain. Hal ini cukup sederhana apabila dilakukan pada suatu jaringan dengan jumlah _Router_ dan segmen jaringan yang sedikit, namun akan bertambah sulit dan melelahkan seiring bertambahnya jumlah dan kompleksitas jaringan. Tak hanya itu, apabila ada segmen jaringan baru, seluruh _Router_ harus diberi tahu mengenai hal ini yang tentunya sangat tidak efisien dan akan menghabiskan banyak waktu.

Namun, karena kita akan menggunakan topologi sederhana yang tidak banyak berubah dengan segmen jaringan yang sedikit, maka Static Routing dapat mempermudah kita karena kita akan memiliki kontrol penuh terhadap topologi kita. Selanjutnya, kita akan mencoba melakukannya di dalam GNS3.

![image](../Modul-2/images/static_routing.webp)

### 1.2 Implementasi

Tentu saja kita akan dapat lebih mudah memahaminya dengan langsung melakukan implementasi. Untuk itu, kita akan menggunakan topologi seperti berikut:

![image](../Modul-2/images/topologi.jpg)

Kemudian, pembagian IP Address untuk kasus ini adalah sebagai berikut:

```
# Kompleks A: 10.40.1.X
eth0 Water7: 10.40.1.1
EniesLobby: 10.40.1.2
Westalis: 10.40.1.3

# Kompleks Jalanan Kiri: 10.40.2.X
eth1 Water7: 10.40.2.1
eth0 Dressrosa: 10.40.2.2

# Kompleks Jalanan Kanan: 10.40.3.X
eth1 Dressrosa: 10.40.3.1
eth0 Foosha: 10.40.3.2

# Kompleks B: 10.40.4.X
eth1 Foosha: 10.40.4.1
Alabasta: 10.40.4.2

# Kompleks C: 10.40.5.X
eth2 Foosha: 10.40.5.1
Jipangu: 10.40.5.2
Loguetown: 10.40.5.3
```

Untuk kasus ini, set semua netmask menjadi `255.255.255.0` dahulu (penjelasan lebih lanjut ada di Modul 4). Kemudian, untuk semua host (semua node kecuali Water7, Dressrosa, dan Foosha), set gateway nya menjadi IP Address _Router_ yang terhubung. Misal, EniesLobby dan Water7 terhubung pada eth0 Water7, maka gateway untuk mereka berdua adalah `10.40.1.1`. Lalu, set gateway untuk router sebagai berikut:

```
# kosongi gateway untuk interface eth0 Water7 dan interface eth1 & eth2 Foosha
eth1 Water7: 10.40.2.2
eth0 Foosha: 10.40.3.1
# kosongi juga gateway Dressrosa untuk semua interface
```

#### 1.2.1 Sebelum Routing

Apabila sudah selesai melakukan konfigurasi, lakukan tes konektivitas dengan melakukan ping antar-node di dalam segmen jaringan (kompleks) yang sama. Misal, pastikan **Westalis** bisa melakukan ping ke **EniesLobby** dan **Water7**, **Dressrosa** bisa melakukan ping ke **Foosha**, dan lain-lain

> Kiat: Tidak perlu masuk ke seluruh node. Cukup masuk ke **Water7** dan **Foosha** dan ping semua perangkat yang terhubung dengannya, karena pada umumnya ping bersifat simetris (apabila host A bisa melakukan ping ke host B, berlaku pula sebaliknya) kecuali ada konfigurasi tambahan seperti Firewall atau NAT.

Sekarang, silahkan kalian coba melakukan ping antar-kompleks dengan jarak yang cukup jauh Contohnya **Dressrosa** (`10.40.3.1`) ke **Alabasta** (`10.40.4.2`). Seharusnya, kalian akan mendapatkan output seperti berikut

![image](../Modul-2/images/dressrosa_alabasta_before_v1.jpg)

atau

![image](../Modul-2/images/dressrosa_alabasta_before_v2.jpg)

Kalian mungkin tidak mendapat output sama persis di atas (bahkan _hang_ ketika melakukan ping). Namun, pada hakikatnya hasilnya adalah sama, yaitu tidak bisa melakukan ping.

> Jika melakukan ping antar-kompleks yang masih berdekatan dan terhubung ke _router_ yang sama (misalnya kompleks B dan kompleks C), ada kemungkinan komunikasi ping masih tetap dapat dilakukan.

Maka dari itu, kita akan memberi tahu _router_ bagaimana cara mencapai kompleks lain.

#### 1.2.2 Proses Routing

Cara yang pertama cukup sederhana, yaitu hanya memberi tahu **Dressrosa** tentang bagaimana cara mencapai semua segmen jaringan. Untuk itu, kita akan menggunakan perintah berikut:

```
ip route add <SUBNET TUJUAN>/<NETMASK> via <GATEWAY>
```

Penjelasan:

- **SUBNET TUJUAN** berisi NID atau segmen jaringan yang akan dituju. Di kasus kita, 3 oktet pertama berisi IP kompleks dan oktet terakhir berisi `0`. Penjelasan lebih detail akan kita telusuri di modul 4.

- **NETMASK** untuk kasus kita akan diisi `24` saja.

- **GATEWAY** berisi IP Address dari _router_ untuk menuju ke segmen jaringan tersebut.

Misal:

```
ip route add 10.40.5.0/24 via 10.40.3.2
```

Ketika kita menjalankan perintah di atas pada **Dressrosa**, kita seolah-olah memberi tahu dia bahwa, "Halo Dressrosa, kalau ada _packet_ dengan IP Address tujuan di kompleks C (`10.40.5.X`), maka cara meraihnya adalah dengan melewati `10.40.3.2` (yaitu **Foosha**)"

![image](../Modul-2/images/dressrosa_after.jpg)

Sebelum menjalankan perintah di atas, Dressrosa belum tau cara mencapai kompleks C (`10.40.5.X`). Maka dari itu, dia mengatakan bahwa jaringan tersebut tidak dapat diraih (`Network is unreachable`). Setelah kita beri tahu, maka sekarang dia memiliki informasi cara menuju segmen jaringan tersebut. Sehingga, dia dan perangkat luar yang terhubung secara langsung dengan dia (dalam kasus ini, **Water7**) bisa menghubungi alamat yang berada di segmen jaringan tersebut.

![image](../Modul-2/images/water7_ok.jpg)

Namun, kompleks A masih belum bisa melakukan ping kepada kompleks C. Walaupun packet yang dikirimkan dari kompleks A (`10.40.1.X`) bisa sampai pada kompleks C (`10.40.5.X`) karena semua _router_ sudah tahu jalannya, mereka masih belum tahu cara meraih kompleks A untuk balasannya. Maka dari itu, kita perlu memberi tahu **Dressrosa** lagi mengenai cara meraih kompleks A dengan menjalankan perintah berikut:

```
ip route add 10.40.1.0/24 via 10.40.2.1
```

![image](../Modul-2/images/enieslobby_before_after.jpg)

Seperti pada gambar, awalnya **EniesLobby** sebagai penduduk kompleks A tidak dapat menghubungi kompleks C. Setelah kita jalankan perintah di atas pada **Dressrosa**, EniesLobby dapat melakukan _ping_ ke kompleks C.

> Lakukan hal yang sama untuk kompleks B. Apakah kalian tahu _command_ yang digunakan dan di node apa?

#### 1.2.3 Setelah Routing

Mungkin sembari melakukan implementasi, ada beberapa pertanyaan yang muncul di benak kalian, seperti:

- _Mengapa hanya **Dressrosa** yang dikonfigurasi, sedangkan ada 3 router?_

- _Apakah berarti **Water7** dan **Foosha** tahu cara meraih semua kompleks yang ada? Bagaimana bisa?_

- _Apa yang akan terjadi apabila ada kompleks baru yang ditambahkan, seperti mungkin kompleks D di `10.40.6.X`?_

Potongan _puzzle_ terakhir yang membuat ini semua terjadi dan bisa menjawab kalian adalah **Default Gateway**.

Sederhananya, Default Gateway bisa diibaratkan sebagai orang yang kalian tanya (jika mengikuti ilustrasi awal, maka diibaratkan kantor pos perumahan) ketika tidak tahu arah. Ketika ada _packet_ dengan tujuan yang asing, alih-alih menyerah dan membuangnya, kita cukup kirimkan _packet_ tersebut ke Default Gateway. Hal inilah yang terjadi pada implementasi di atas dan mengapa hanya **Dressrosa** yang dikonfigurasi.

**Study Case: Ping dari EniesLobby ke Jipangu**

Jalankan _command_ di bawah ini di **EniesLobby**:

```
mtr 10.40.5.2
```

![image](../Modul-2/images/mtr.jpg)

melalui `mtr`, kita bisa mengetahui _packet_ yang kita kirimkan melalui node mana saja. Perintah ini mirip seperti `traceroute`. Untuk kasus ini, kita ingin mencari tahu jalur mana yang ditempuh _packet_ dari **EniesLobby** (`10.40.1.3`) ke **Jipangu** (`10.40.5.2`). Penjelasan alur rute yaitu sebagai berikut:

1. Pertama, karena IP Address tujuan (yaitu `10.40.5.2`) tidak berada pada segmen jaringan yang sama, **EniesLobby** tidak tahu bagaimana cara mengirimkannya. Namun karena dia memiliki Default Gateway yaitu **Water7**, maka dia cukup mengirimkan _packet_ tersebut ke sana (yaitu `10.40.1.1`)

2. Hal yang sama dialami oleh **Water7**. Dia sebenarnya juga tidak tahu bagaimana cara mengirimkan _packet_ ini, namun karena dia juga memiliki Default Gateway yaitu **Dressrosa**, maka dia akan mengirimkannya ke sana (yaitu `10.40.2.2`)

3. Tidak seperti EniesLobby dan Water7, **Dressrosa** tahu bagaimana cara meraih segmen jaringan dari tujuan _packet_ ini. Menurut data yang dia punya (atau _Routing Table_ nya), untuk meraih packet tersebut dia harus mengirimkannya ke **Foosha**, yaitu `10.40.3.2`

4. Terakhir, karena **Foosha** terhubung secara langsung dengan **Jipangu**, maka _packet_ bisa langsung dikirimkan ke tujuan, yaitu `10.40.5.2`.

> Seandainya **Water7** dan **Foosha** tidak memiliki Default Gateway, maka kita harus menjalankan perintah `ip route add` untuk mengisi _Routing Table_ mereka seperti apa yang kita lakukan pada **Dressrosa**.

#### 1.2.4 Routing Table

Alangkah baiknya apabila kita tutup dengan penjelasan singkat mengenai komponen terakhir pada perjalanan kita, yaitu **Routing Table**.

Routing Table adalah suatu struktur data yang menyimpan informasi mengenai cara meraih suatu segmen jaringan. Biasanya, di dalam suatu Routing Table terdapat informasi mengenai segmen jaringan tujuan, _next hop_ atau _gateway_ nya, _network interface_ yang digunakan, dan informasi tambahan.

Mari kita lihat Routing Table pada **Dressrosa** dengan:

```
ip route show
```

![image](../Modul-2/images/routing_table.jpg)

- `10.40.1.0/24 via 10.40.2.1 dev eth0`: Untuk meraih segmen jaringan `10.40.1.0/24`, maka kirim _packet_ ke `10.40.2.1` melalui _interface_ `eth0`.

- `10.40.2.0/24 dev eth0 proto kernel scope link src 10.40.2.2`: Segmen jaringan `10.40.2.0/24` terhubung secara langsung melalui _interface_ `eth0`. IP milik **Dressrosa** sendiri pada hubungan ini adalah `10.40.2.2`

> Bisakah kalian menerjemahkan sisanya?

### 1.3 Troubleshooting

Apabila kalian sudah melakukan konfigurasi pada _router_ namun masih belum bisa melakukan ping, ada kemungkinan _router_ kalian tidak mau meneruskan _packet_ (_packet forwarding_ dinonaktifkan)

Untuk mengeceknya, bisa menjalankan:

```
cat /proc/sys/net/ipv4/ip_forward
```

`1` berarti _packet forwarding_ aktif dan `0` berarti nonaktif. Untuk mengaktifkannya, bisa menjalankan:

```
sysctl -w net.ipv4.ip_forward=1
```

atau dengan memastikan `net.ipv4.ip_forward=1` ada (biasa di-comment) di dalam `/etc/sysctl.conf`. Restart dengan

```
sysctl -p
```
# Modul 3: Reliable Data Transfer TCP & Static Routing

Panduan belajar praktikum. Fokus: **prediksi, pertanyaan beserta jawabannya, filter Wireshark dan hasil yang seharusnya, koreksi laporan, troubleshooting**.

Prefix IP: **10.14**. Angka contoh (ISN, RTT, throughput) berasal dari lab sendiri dan **akan berbeda** saat praktikum. Pahami caranya, jangan hafal angkanya.

Catatan penulisan file: semua pembuatan file di panduan ini memakai **nano** (simpan `Ctrl+O`, Enter; keluar `Ctrl+X`). Kalau nano belum ada: `apk add nano` (butuh internet).

---

## Daftar Isi

1. Peta topologi
2. Tutorial persiapan
3. Capture Wireshark di GNS3
4. Tabel filter Wireshark
5. Bagian A: prediksi, tabel, pertanyaan
6. Bagian B: prediksi, tabel, pertanyaan
7. Bagian C: prediksi, tabel, pertanyaan
8. Bagian D: prediksi, tabel, pertanyaan
9. Bagian E: prediksi, tabel, pertanyaan
10. Pertanyaan lisan yang mungkin ditanyakan
11. Koreksi laporan (temuan dari file laporan)
12. Troubleshooting
13. Checklist bukti

---

## 1. Peta Topologi

| Node | Peran | IP |
|---|---|---|
| RhodesIsland | Router ke NAT + titik gangguan `tc` | eth0 DHCP (NAT), eth1 10.14.1.1/24, eth2 10.14.2.1/24 |
| Lungmen | Router | eth0 10.14.1.2/24 (gw 10.14.1.1), eth1 10.14.3.1/24 |
| Ursus | Router | eth0 10.14.2.2/24 (gw 10.14.2.1), eth1 10.14.4.1/24 |
| Victoria | DHCP server subnet 3.0/24 | eth0 10.14.3.2/24, pool 10.14.3.100-120 |
| Yan | DHCP server subnet 4.0/24 | eth0 10.14.4.2/24, pool 10.14.4.50-60 |
| Laterano | Web server, sender, capture sisi server | 10.14.4.15 (reservation Yan) |
| Columbia | Client (curl, iperf3), capture sisi client | 10.14.3.100 (DHCP Victoria) |

Jalur: Columbia -> Switch1 -> Lungmen -> RhodesIsland -> Ursus -> Hub1 -> Laterano. Ada **3 router** di antara keduanya, jadi TTL turun dari 64 ke **61**.

Arah gangguan `tc`: `netem` bekerja di **egress** (paket keluar interface).
- Columbia -> Laterano (data request) keluar RhodesIsland lewat **eth2**.
- Laterano -> Columbia (data respons) keluar RhodesIsland lewat **eth1**.
Jadi tiap arah melewati **satu** netem. Loss 5% berarti sekitar 5% per arah, bukan 10%. Delay 100 ms per arah, RTT bertambah sekitar 200 ms.

---

## 2. Tutorial Persiapan

### 2.1 IP statis router

Edit dengan nano:
```
nano /etc/network/interfaces
```

RhodesIsland:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 10.14.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.14.2.1
    netmask 255.255.255.0
```

Lungmen:
```
auto eth0
iface eth0 inet static
    address 10.14.1.2
    netmask 255.255.255.0
    gateway 10.14.1.1

auto eth1
iface eth1 inet static
    address 10.14.3.1
    netmask 255.255.255.0
```

Ursus:
```
auto eth0
iface eth0 inet static
    address 10.14.2.2
    netmask 255.255.255.0
    gateway 10.14.2.1

auto eth1
iface eth1 inet static
    address 10.14.4.1
    netmask 255.255.255.0
```

### 2.2 Static routing dan NAT

```
# RhodesIsland
sysctl -w net.ipv4.ip_forward=1
ip route add 10.14.3.0/24 via 10.14.1.2 dev eth1
ip route add 10.14.4.0/24 via 10.14.2.2 dev eth2
iptables -t nat -A POSTROUTING -j MASQUERADE -s 10.14.0.0/16 -o eth0

# Lungmen
sysctl -w net.ipv4.ip_forward=1
ip route add 10.14.2.0/24 via 10.14.1.1 dev eth0
ip route add 10.14.4.0/24 via 10.14.1.1 dev eth0

# Ursus
sysctl -w net.ipv4.ip_forward=1
ip route add 10.14.1.0/24 via 10.14.2.1 dev eth0
ip route add 10.14.3.0/24 via 10.14.2.1 dev eth0

# Verifikasi di tiap router
ip route show
```

- `ip_forward=1`: izinkan kernel meneruskan paket antar interface (inilah yang membuat node jadi router). Tanpa ini ping ke router tetangga berhasil, tetapi ping ke subnet seberang gagal.
- `MASQUERADE`: ganti alamat sumber paket internal dengan alamat `eth0` RhodesIsland saat keluar ke internet (NAT).
- `File exists` pada `ip route add` aman diabaikan.
- `Nexthop has invalid gateway`: gateway tidak satu subnet dengan IP interface. Cek `ip -br addr`.
- Semua `ip ...`, `sysctl -w`, `iptables` **tidak permanen**. Simpan di script setup per node.

Script setup RhodesIsland (buat dengan nano):
```
nano /root/setup.sh
```
Isi:
```
#!/bin/sh
sysctl -w net.ipv4.ip_forward=1
ip route add 10.14.3.0/24 via 10.14.1.2 dev eth1
ip route add 10.14.4.0/24 via 10.14.2.2 dev eth2
iptables -t nat -A POSTROUTING -j MASQUERADE -s 10.14.0.0/16 -o eth0
```
Lalu `chmod +x /root/setup.sh` dan jalankan `/root/setup.sh`.

### 2.3 DNS di semua node

```
echo "nameserver 8.8.8.8" > /etc/resolv.conf
ping -c 3 google.com
```
`>` menimpa isi berkas. `ping 8.8.8.8` berhasil tapi `ping google.com` gagal berarti masalah DNS. Kalau dua-duanya gagal, masalah jalur internet.

### 2.4 DHCP dengan Kea

Pasang (butuh internet): `apk add kea-dhcp4` (Alpine) atau `apt install -y kea-dhcp4-server` (Debian/Ubuntu).

Edit config dengan nano:
```
nano /etc/kea/kea-dhcp4.conf
```

Yan:
```json
{
  "Dhcp4": {
    "interfaces-config": { "interfaces": ["eth0"] },
    "lease-database": { "type": "memfile", "persist": true, "name": "/var/lib/kea/kea-leases4.csv" },
    "valid-lifetime": 3600,
    "subnet4": [
      {
        "id": 1,
        "subnet": "10.14.4.0/24",
        "pools": [ { "pool": "10.14.4.50 - 10.14.4.60" } ],
        "option-data": [
          { "name": "routers", "data": "10.14.4.1" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ],
        "reservations": [
          { "hw-address": "<MAC eth0 Laterano>", "ip-address": "10.14.4.15" }
        ]
      }
    ]
  }
}
```
Victoria: subnet `10.14.3.0/24`, pool `10.14.3.100 - 10.14.3.120`, router `10.14.3.1`, tanpa reservation.

MAC Laterano: `ip link show eth0` di Laterano (baris `link/ether`).

Jalankan (container tidak punya systemd):
```
mkdir -p /var/lib/kea /run/kea /var/log/kea
kea-dhcp4 -t /etc/kea/kea-dhcp4.conf     # tes sintaks, harus tanpa error
kea-dhcp4 -c /etc/kea/kea-dhcp4.conf &   # jalankan
ss -ulnp | grep ':67'                    # port 67 harus muncul
```
Client: `udhcpc -i eth0 -v`. Hasil benar: `lease of 10.14.4.15 obtained from 10.14.4.2`.

`mkdir -p` tidak error kalau direktori sudah ada dan tidak menghapus isinya. Pastikan seluruh perintah `mkdir` ada dalam **satu baris**.

### 2.5 Web server di Laterano

```
apk update
apk add python3
python3 --version
mkdir -p /root/myscripts/myweb
nano /root/myscripts/start_http.sh
```
Isi script:
```
#!/bin/sh

mkdir -p /root/myscripts/myweb
cd /root/myscripts/myweb

python3 -m http.server 9999 -d /root/myscripts/myweb
```
Simpan, keluar, lalu:
```
chmod +x /root/myscripts/start_http.sh
ls -l /root/myscripts
```
Harus berupa berkas (`-rwx...`), bukan direktori (`d...`). Salah ketik `mkdir /root/myscripts/start_http.sh` membuat **direktori**; hapus dengan `rmdir /root/myscripts/start_http.sh`.

Berkas uji (perintah terminal, **bukan** isi file; jalankan di `/root/myscripts/myweb`):
```
cd /root/myscripts/myweb
dd if=/dev/random of=tes1000.bin   bs=1    count=1000
dd if=/dev/random of=tes10000.bin  bs=10   count=1000
dd if=/dev/random of=tes50000.bin  bs=50   count=1000
dd if=/dev/random of=tes5M.bin     bs=5000 count=1000
ls -l
```
Kalau `/dev/random` macet karena entropi, ganti dengan `/dev/urandom`.

Jalankan server supaya tidak mati bersama terminal:
```
nohup /root/myscripts/start_http.sh > /tmp/http.log 2>&1 &
ss -tlnp | grep 9999
nano -v /tmp/http.log
```
(`nano -v` = mode baca saja.) Atau pakai tmux: `tmux`, split `Ctrl+b` lalu `%` (kiri/kanan) atau `"` (atas/bawah), pindah pane dengan `Ctrl+b` lalu panah.

### 2.5.1 Perintah curl standar

```
curl -sS -o /dev/null -w "size=%{size_download}B speed=%{speed_download}B/s total=%{time_total}s\n" http://10.14.4.15:9999/<berkas>
```
- `-o /dev/null` buang isi, `-s` senyap, `-S` tetap tampilkan error, `-w` cetak ringkasan.
- `-o` tidak boleh terlewat (tanpa itu `/dev/null` dibaca sebagai URL).
- Jangan menulis `[IP Prefix]` atau tanda `[` di URL (notasi IPv6).
- `size=0B` = gagal connect. Pakai `-sS` untuk melihat penyebabnya.

### 2.6 Verifikasi awal dari Columbia

```
ip -br addr
ping -c 3 10.14.4.15
curl -sS -o /dev/null -w "size=%{size_download}B speed=%{speed_download}B/s total=%{time_total}s\n" http://10.14.4.15:9999/tes10000.bin
```
Firefox: `http://10.14.4.15:9999` menampilkan listing keempat berkas.

---

## 3. Capture Wireshark di GNS3

1. Klik kanan **link** (Columbia-Switch1 untuk sisi client, Hub1-Laterano untuk sisi server) -> **Start capture**.
2. Filter `tcp.port == 9999` lalu **Enter** (hijau = valid, merah = salah).
3. **Mulai capture sebelum curl.** Satu capture berisi satu download.
4. Setelah curl selesai: Stop (`Ctrl+E`), Save (`Ctrl+S`). Capture GNS3 lewat *standard input*, **hilang kalau jendela ditutup tanpa disimpan** (judul bertanda `*` = belum disimpan).
5. Capture bersih: di GNS3 klik kanan link -> Stop capture, lalu Start capture lagi.

Pengaturan yang membantu:
- Edit > Preferences > Protocols > TCP: matikan *Relative sequence numbers*, atau baca `Sequence Number (raw)`.
- **Apply as Column**: klik field di panel detail -> klik kanan. Tambahkan Seq, Ack, Len, `The RTT to ACK the segment was`.
- Status bar (paling bawah): `Displayed: N` = jumlah paket lolos filter. **Wajib ikut di-screenshot.**
- View > Status Bar kalau tidak tampak. Panel detail kecil: seret pemisahnya ke atas.

Pembagian capture sesuai soal:
- **A:** nomor 1-8 Laterano, 9-10 Columbia.
- **B:** nomor 2 dan 5 Columbia, selain itu Laterano.
- **C:** nomor 4, 5, 9 Columbia, selain itu Laterano.

---

## 4. Tabel Filter Wireshark

| Tujuan | Filter | Hasil yang diharapkan |
|---|---|---|
| Koneksi uji | `tcp.port == 9999` | Semua paket download |
| Satu koneksi | `tcp.port == 40454` (port klien dari kolom Info) | Satu download |
| Handshake | `tcp.flags.syn == 1` | SYN dan SYN-ACK |
| Data server -> client | `ip.src == 10.14.4.15 && tcp.len > 0` | Segmen data |
| Paket client | `ip.src == 10.14.3.100` | GET dan ACK |
| Semua retransmisi | `tcp.analysis.retransmission` | A: 0. B dan C: > 0 |
| Fast retransmission | `tcp.analysis.fast_retransmission` | Label `[TCP Fast Retransmission]` |
| Duplicate ACK | `tcp.analysis.duplicate_ack` | `[TCP Dup ACK nnnn#1]`, `#2`, `#3` ... |
| Segmen hilang tak tertangkap | `tcp.analysis.lost_segment` | `Previous segment not captured` |
| RTT | `tcp.analysis.ack_rtt` | ACK yang punya data RTT |
| Bytes in flight | `tcp.analysis.bytes_in_flight` | Proksi cwnd |
| Penutupan | `tcp.flags.fin == 1` | FIN-ACK |
| Window receiver | `ip.src == 10.14.3.100 && tcp.port == 9999` | Untuk I/O Graph window |
| Satu seq spesifik | `tcp.seq == 851631` | Segmen asli + retransmisinya |

Aturan:
- Jangan menulis placeholder `<angka>` lengkap dengan `<>`. Tulis angkanya saja.
- Nilai `tcp.seq` di filter mengikuti mode tampilan (relatif atau raw). Konsisten.

---

## 5. Bagian A: Transfer Normal (Skenario 1)

### Persiapan
`tc qdisc show dev eth1` dan `eth2` harus bersih (tanpa `netem`/`tbf`). Kalau ada: `tc qdisc del dev eth1 root` dan `tc qdisc del dev eth2 root`. `Cannot delete qdisc with handle of zero` = memang tidak ada aturan, aman.

### Prediksi (A.4.1), tulis sebelum melihat capture
> ISN client pada SYN **bukan 0**, melainkan nilai acak 32-bit (0 sampai 4.294.967.295). TCP memilih ISN acak untuk mencegah serangan sequence number prediction/spoofing dan agar segmen lama dari koneksi sebelumnya tidak tercampur dengan koneksi baru (RFC 6528). Wireshark akan menampilkan `Seq=0` hanya karena relative sequence number.

Prediksi tambahan yang aman ditulis:
> Handshake terdiri dari 3 paket (SYN, SYN-ACK, ACK). ACK naik kumulatif, tidak ada retransmisi, RTT beberapa milidetik, dan koneksi ditutup lewat FIN/ACK.

### Tabel A.4.2: apa yang dicari

| No | Parameter | Cara baca | Hasil seharusnya |
|---|---|---|---|
| 1 | IP sumber/tujuan | Paket SYN, bagian IP | Src 10.14.3.100, Dst 10.14.4.15, port tujuan 9999, port sumber acak |
| 2 | Handshake | 3 paket pertama | SYN, SYN-ACK, ACK |
| 3 | ISN client | `Sequence Number (raw)` pada SYN | Angka besar acak |
| 4 | ISN server | `Sequence Number (raw)` pada SYN-ACK | Beda dari ISN client |
| 5 | ACK | `Ack` pada SYN-ACK dan ACK ketiga | SYN-ACK: ISN client + 1. ACK ketiga: ISN server + 1 |
| 6 | SEQ berikutnya | Dua segmen data berurutan di tengah stream | seq berikutnya = seq + len (mis. 3101 + 1448 = 4549) |
| 7 | Pola ACK | ACK client | Naik kumulatif, tanpa duplikat |
| 8 | RTT | Nilai `tcp.analysis.ack_rtt` pada satu ACK (Laterano) | Beberapa ms |
| 9 | Retransmisi | `tcp.analysis.retransmission` (Columbia) | `Displayed: 0` |
| 10 | Penutupan | `tcp.flags.fin == 1` (Columbia) | FIN-ACK, ACK, FIN-ACK, ACK (bisa tergabung jadi 3 paket) |

Rumus:
- Handshake: SYN memakai 1 nomor urut, jadi Ack = ISN + 1.
- GET 91 byte -> server membalas Ack = 1 + 91 = 92 (relatif).
- ACK akhir relatif file 10000 B dengan header HTTP 204 B = 1 + 204 + 10000 = 10205.
- Payload umum 1448 B (MSS 1460 dikurangi opsi timestamp). Segmen data tes5M sekitar 5.000.000 / 1448 = 3450.
- ISN dari hex: 4 byte setelah port tujuan pada header TCP. Contoh `c5 34 16 03` = 0xC5341603 = 3308525059.
- Nomor `[TCP Dup ACK 1927#1]`: **1927 adalah nomor frame**, bukan nomor Ack.

### Pertanyaan A.4.2 dan jawaban

1. **Fungsi sequence dan acknowledgment number?**
   Sequence number memberi nomor urut tiap byte sehingga penerima dapat mengurutkan data dan mendeteksi hilang/duplikat. Acknowledgment number menyatakan byte berikutnya yang diharapkan sekaligus mengonfirmasi semua byte sebelumnya diterima. Contoh: GET 91 byte dijawab Ack=92.
2. **Kenapa ACK kumulatif?**
   Efisien (satu ACK untuk banyak segmen), tahan terhadap ACK hilang (ACK berikutnya mencakup yang sebelumnya), sederhana (satu angka). Kekurangannya tidak memberi tahu segmen setelah celah; dilengkapi opsi SACK.
3. **Jika segmen hilang di tengah?**
   Penerima mengirim duplicate ACK untuk byte yang hilang. Pengirim fast retransmit setelah 3 dupACK, atau retransmit saat RTO habis, lalu cwnd dikecilkan. Setelah segmen tiba, ACK kumulatif melompat maju dan data tersusun benar.
4. **Bukti reliable di Wireshark?**
   Handshake sukses, seq berurutan (seq + len = seq berikutnya), ACK kumulatif naik tanpa duplikat, retransmisi = 0, `size` curl sama dengan ukuran file, koneksi ditutup lewat FIN/ACK.

Pertanyaan tambahan dari instruksi A:
- **Kenapa ISN acak, bukan nol?** Keamanan (mencegah tebakan seq oleh penyerang) dan mencegah segmen lama (delayed duplicate) tercampur dengan koneksi baru.
- **Kenapa timer retransmisi terlalu pendek atau terlalu panjang sama-sama bermasalah?** Pendek: timer habis sebelum ACK tiba, retransmisi tidak perlu (spurious), bandwidth terbuang, cwnd turun tanpa alasan. Panjang: saat segmen benar-benar hilang, pengirim menunggu lama, link menganggur, transfer lambat. TCP menghitung RTO adaptif: SRTT + 4 x RTTVAR, sedikit di atas RTT.

---

## 6. Bagian B: Packet Loss 1% (Skenario 2)

### Setup
```
tc qdisc del dev eth1 root
tc qdisc del dev eth2 root
tc qdisc add dev eth1 root netem loss 1%
tc qdisc add dev eth2 root netem loss 1%
tc qdisc show dev eth1
tc qdisc show dev eth2
```
Harus tampil `netem ... loss 1%`. `Exclusivity flag on` = sudah ada qdisc, `del` dulu.

Baseline untuk perbandingan: ukur **`tes5M.bin` tanpa loss** (bukan `tes10000.bin`), karena throughput bergantung ukuran file.

### Prediksi (B.1), tulis sebelum melihat capture
> Saat segmen hilang, penerima (Columbia) tidak mengirim NAK, melainkan **duplicate ACK**: ACK bernomor sama berulang untuk byte yang masih ditunggu, tiap kali menerima segmen yang datang setelah celah. Setelah 3 dupACK, pengirim (Laterano) melakukan **fast retransmit** tanpa menunggu timeout. Segmen retransmisi punya seq sama dengan segmen asli. Throughput turun dan waktu transfer naik karena TCP mengecilkan congestion window setelah loss.

Prediksi tambahan:
> Karena loss hanya 1% dan data terus mengalir, dupACK cukup banyak sehingga timeout retransmission kemungkinan **tidak muncul**.

### Tabel B.3: apa yang dicari

| No | Capture | Filter / cara | Hasil seharusnya |
|---|---|---|---|
| 1 | - | `tc qdisc show` | `loss 1%` di eth1 dan eth2 |
| 2 | Columbia | `tcp.analysis.duplicate_ack` | Kelompok dupACK dengan Ack identik, tanpa NAK. Tampilkan kolom Ack |
| 3 | Laterano | `tcp.analysis.retransmission` | `Displayed: N` > 0. **Tulis angkanya** |
| 4 | Laterano | Klik satu retransmisi, catat seq dan len, lalu `tcp.seq == <seq>` | Dua paket seq dan len sama. **Len maksimal sekitar 1448**, bukan angka sebesar jutaan |
| 5 | Columbia | Satu kelompok dupACK `#1`..`#3`+ | Minimal 3 dupACK berturut-turut. Tulis total dari status bar |
| 6 | Laterano | Round Trip Time graph, atau kolom RTT | RTT lonjakan di sekitar loss. Tulis nilai terendah dan tertinggi |
| 7 | Laterano | dupACK ke-3 dan fast retransmission yang menyusul | Selisih sekitar 1 ms (jauh di bawah 200 ms) |
| 8 | Laterano | Retransmisi dengan jeda >= 200 ms dari aslinya | Kalau tidak ada: jelaskan semua loss dipulihkan lewat dupACK |
| 9 | Columbia | curl `speed_download` | Lebih rendah dari baseline |
| 10 | Columbia | curl `time_total` | Lebih lama dari baseline |
| 11 | Laterano | I/O Graph `MAX(tcp.analysis.bytes_in_flight)`, filter `ip.src == 10.14.4.15 && tcp.port == <port>`, interval 10 ms | Naik, turun tajam setelah loss, naik lagi |

Jebakan penting:
- Cara mencari dupACK ke-3: lihat label `#3` pada Info. Lalu `Ctrl+T` (time reference) atau kurangkan `frame.time_relative` manual dengan paket fast retransmission yang menyusul.
- dupACK `#4`, `#5`, dan seterusnya wajar muncul setelah retransmisi (segmen lama masih berdatangan di luar urutan).
- `tcp.analysis.rto` adalah **nama field yang menyesatkan**. Nilai "RTO for this segment" di *SEQ/ACK analysis* hanyalah selisih waktu dari segmen asli ke retransmisi, **bukan timer RTO Linux**. Retransmisi non-fast dengan selisih 0,5 ms bukan timeout; itu retransmisi pada fase recovery (SACK).
- Timeout sungguhan: jeda dari paket asli >= sekitar 200 ms tanpa 3 dupACK sebelumnya.
- Tidak munculnya timeout adalah hasil sah. Jelaskan alasannya (soal sendiri meminta hal ini).
- `bytes_in_flight` adalah **proksi**, bukan cwnd langsung. Tulis "perkiraan".
- Contoh hasil lab: tanpa loss 18.320.588 B/s dan 0,273 s; loss 1% 8.790.513 B/s dan 0,569 s.

### Pertanyaan B.3 dan jawaban

1. **Bagaimana TCP tahu segmen hilang?** Lewat duplicate ACK (penerima mengirim ACK bernomor sama karena menerima segmen di luar urutan) dan lewat timeout RTO (ACK tidak datang sampai timer habis). TCP tidak memakai NAK.
2. **Timeout retransmission vs fast retransmit?** Fast retransmit dipicu 3 dupACK, retransmisi sekitar 1 RTT, cwnd turun sekitar setengah. Timeout menunggu RTO (minimum Linux sekitar 200 ms), dipakai saat dupACK tidak cukup (mis. segmen terakhir hilang atau retransmisi ikut hilang), cwnd turun ke 1 MSS dan masuk slow start. Timeout lebih lambat dan lebih merugikan.
3. **Kenapa throughput turun saat loss?** Loss dianggap tanda congestion, cwnd dan threshold dikecilkan sehingga data per RTT berkurang; ada waktu recovery; byte setelah celah tertahan sampai segmen hilang tiba (head-of-line blocking).
4. **Bagaimana Wireshark menunjukkan TCP tetap reliable?** Retransmisi punya seq dan len sama dengan segmen asli, ACK kumulatif melompat maju setelah segmen pengganti tiba, dan curl tetap `size=5000000B`. Hanya waktu transfer yang bertambah.

---

## 7. Bagian C: CUBIC vs Reno (Skenario 3)

### Setup (sesuai modul: tbf root, netem child)
```
tc qdisc del dev eth1 root
tc qdisc del dev eth2 root
tc qdisc add dev eth1 root handle 1: tbf rate 2mbit burst 32kbit latency 100ms
tc qdisc add dev eth1 parent 1:1 handle 10: netem delay 100ms loss 1%
tc qdisc add dev eth2 root handle 1: tbf rate 2mbit burst 32kbit latency 100ms
tc qdisc add dev eth2 parent 1:1 handle 10: netem delay 100ms loss 1%
tc qdisc show dev eth1
tc qdisc show dev eth2
```
Cek: `ping -c 5 10.14.4.15` dari Columbia harus sekitar **200 ms**.

Algoritma di **Laterano**:
```
# CUBIC
ip tcp_metrics flush
sysctl -w net.ipv4.tcp_no_metrics_save=1
sysctl -w net.ipv4.tcp_congestion_control=cubic
sysctl net.ipv4.tcp_congestion_control

# Reno
sysctl -w net.ipv4.tcp_congestion_control=reno
ip tcp_metrics flush
sysctl net.ipv4.tcp_congestion_control
```
Berlaku untuk koneksi **baru**. Jalankan di terminal kedua kalau terminal pertama dipakai server. Server HTTP tidak perlu restart.

Instal semua paket **sebelum** memasang gangguan: `tc` dengan delay dan rate 2 Mbps membuat `apk` sangat lambat.

### Prediksi (C.1), tulis sebelum melihat grafik
> **Reno:** cwnd tumbuh linear (additive increase, +1 MSS per RTT), turun separuh saat loss (multiplicative decrease), pola gergaji (sawtooth).
> **CUBIC:** cwnd tumbuh mengikuti fungsi kubik (cepat setelah loss, melambat mendekati W_max titik loss sebelumnya, lalu cepat lagi), turun hanya sekitar 30% (faktor 0,7), sehingga pulih lebih cepat daripada Reno.

Prediksi hasil tabel:
> Throughput kedua algoritma di bawah batas tbf (2 Mbps = 250.000 B/s) karena loss terus memotong cwnd, dengan CUBIC lebih tinggi dari Reno. RTT rata-rata sekitar 200 ms (dari delay 100 ms x 2), RTT maksimum lebih tinggi karena antrean di tbf. Receive window ditentukan penerima, bukan algoritma.

Dasar perhitungan:
- BDP = 2.000.000 bit/s x 0,2 s = 400.000 bit = 50.000 byte. cwnd jauh di atas itu hanya menumpuk antrean di tbf (RTT naik, paket bisa dibuang).
- Maksimum teoritis 250.000 B/s, jadi 5 MB butuh minimal sekitar 20 detik.

### Tabel C.3: apa yang dicari (gunakan filter, fungsi, dan interval yang **sama** untuk CUBIC dan Reno; satu capture per algoritma)

| No | Cara |
|---|---|
| 1 | Klik paket data server (`10.14.4.15 -> 10.14.3.100`), Statistics > TCP Stream Graphs > Time Sequence (Stevens). Bandingkan dengan prediksi, jelaskan bila beda |
| 2, 3 | Laterano: I/O Graph, filter `tcp.port == 9999 && tcp.analysis.ack_rtt`, Y Field `tcp.analysis.ack_rtt`, **AVG** dan **MAX**, interval besar (10 min) agar satu titik. Klik titik, baca nilai di teks bawah grafik. Satuan detik: kalikan 1000 untuk ms |
| 4, 5 | Columbia: `speed_download` dan `time_total` dari curl |
| 6 | Laterano: `tcp.analysis.retransmission`, Displayed |
| 7 | Columbia: `tcp.analysis.duplicate_ack`, Displayed |
| 8 | Laterano: I/O Graph `MAX(tcp.analysis.bytes_in_flight)`. Tulis deskripsi pola (awal-akhir) dan "perkiraan" |
| 9 | Columbia: I/O Graph `AVG`, filter `ip.src == 10.14.3.100 && tcp.port == 9999`. **Pilih satu field** (`tcp.window_size_value` = nilai mentah header, belum dikali window scale; `tcp.window_size` = nilai sebenarnya) dan konsisten di CUBIC dan Reno. Tulis angka dan satuan byte |
| 10 | Statistics > TCP Stream Graphs > Throughput, deskripsikan konstan atau berfluktuasi |

Aturan:
- **Jangan menyebut grafik Stevens atau `bytes_in_flight` sebagai pengukuran cwnd langsung.** Pakai "menyerupai" atau "perkiraan".
- Hapus grafik sisa di I/O Graph (tombol **-**) supaya skala dan legenda tidak membingungkan. Titik terpecah per menit: naikkan interval.
- Loss acak. Ulangi 2-3 kali kalau selisih kecil, dan sebut itu sebagai batasan.
- Pengukuran cwnd langsung (hanya pembanding pribadi): `ss -tin dst 10.14.3.100` di Laterano saat transfer; baca `cwnd:` (satuan segmen).
- Contoh hasil lab: CUBIC 124.358 B/s, 40,2 s, retransmisi 24; Reno 79.578 B/s, 62,7 s, retransmisi 41.

### Pertanyaan C.3 dan jawaban

1. **Throughput lebih tinggi?** Umumnya CUBIC (di lab: 124.358 vs 79.578 B/s). Setelah loss, Reno memotong cwnd 50% dan tumbuh +1 MSS per RTT (lambat di RTT 200 ms). CUBIC memotong sekitar 30% dan tumbuh kembali dengan fungsi kubik dari waktu sejak loss terakhir. Keduanya di bawah 250.000 B/s karena loss. Hasil satu percobaan; loss acak.
2. **Hubungan retransmisi dan algoritma?** Keduanya loss-based, retransmisi dipicu loss netem. Jumlahnya dipengaruhi lamanya transfer (transfer lebih lama = lebih banyak segmen kena loss) dan agresivitas. Karena loss acak, hubungan sebab-akibat tidak bisa dipastikan dari satu percobaan.
3. **Beda bentuk kurva cwnd?** Reno linear bergerigi (gergaji); CUBIC kubik (cekung lalu cembung di sekitar titik loss sebelumnya). Dengan bottleneck tbf 2 Mbps dan loss, perbedaan di grafik Stevens bisa tipis; grafik hanya menyerupai pola cwnd.
4. **Paling sensitif RTT tinggi?** Reno, karena pertumbuhannya dihitung per RTT. CUBIC bergantung waktu real sejak loss, bukan jumlah RTT.
5. **Lebih efisien di latency tinggi?** Umumnya CUBIC, alasan sama. Sebutkan bukti angka (throughput, waktu, retransmisi).

---

## 8. Bagian D: TCP vs UDP (Loss 5%)

### Setup
```
tc qdisc del dev eth1 root
tc qdisc del dev eth2 root
tc qdisc add dev eth1 root netem loss 5%
tc qdisc add dev eth2 root netem loss 5%
tc qdisc show dev eth1
tc qdisc show dev eth2
```
Pasang iperf3 saat jaringan bersih: `apk add iperf3` di Laterano dan Columbia.
```
# Laterano
iperf3 -s
# Columbia
iperf3 -c 10.14.4.15 -t 10
iperf3 -c 10.14.4.15 -u -b 5M -t 10
```

### Prediksi
> **TCP:** throughput turun dan ada retransmisi (Retr > 0) karena TCP memulihkan loss dan mengecilkan cwnd; data sampai utuh; sender dan receiver hampir sama. **UDP:** bitrate sender tetap sekitar 5 Mbit/s, receiver sedikit lebih rendah, ada Lost/Total sekitar 5%, tidak ada pemulihan, ada jitter.

Karena netem hanya di egress dan tiap arah melewati satu interface, loss UDP yang tercatat sekitar 5%, bukan 10%.

### Isi tabel D

| Sel | Ambil dari |
|---|---|
| Bitrate sender / receiver | Dua baris ringkasan berlabel `sender` dan `receiver` |
| Retr (TCP) | Kolom `Retr` baris sender |
| Lost/Total Datagrams (UDP) | Kolom di ringkasan UDP, ditulis `lost/total (persen)`, mis. `22/432 (5.1%)` |
| Jitter (UDP) | Kolom `Jitter` (ms), tulis lengkap |
| Ringkasan respons terhadap loss | Satu atau dua kalimat per protokol |

Bandingkan angka yang **setara** (sender dengan sender, receiver dengan receiver). UDP dibatasi `-b 5M`, TCP tidak dibatasi, jadi jangan membandingkan 7,86 dengan 5 secara langsung tanpa menyebut itu. Untuk menyebut TCP "turun", sebaiknya ada baseline TCP tanpa loss (opsional: hapus netem, jalankan `iperf3 -c 10.14.4.15 -t 10`, lalu pasang loss lagi).

### Pertanyaan D.3 dan jawaban

1. **Kenapa UDP menampilkan persentase loss, TCP tidak?** iperf3 menyisipkan nomor urut di payload tiap datagram UDP; receiver menghitung celahnya sehingga dapat melaporkan Lost/Total. Pada TCP, loss ditutup retransmisi di kernel sehingga aplikasi tidak pernah melihat data hilang; iperf3 hanya melaporkan jumlah `Retr`.
2. **Kenapa throughput UDP tetap dekat `-b`, TCP turun?** UDP tidak punya congestion control dan retransmisi; sender mengirim sesuai target. Loss hanya terlihat di receiver. TCP mengecilkan cwnd tiap loss sehingga laju kirim tertekan.
3. **Kenapa TCP turun saat loss, UDP tetap mengirim?** TCP menganggap loss sebagai congestion (cwnd dikecilkan, menunggu pemulihan lewat dupACK dan retransmisi). UDP tidak memantau loss dan tidak mengatur laju.
4. **Aplikasi cocok UDP?** Yang mementingkan latensi rendah dan menoleransi loss: VoIP, video call, live streaming, game online, DNS. Data terlambat tidak berguna dan retransmisi TCP menambah delay.

---

## 9. Bagian E: Tiga Ukuran Berkas (Tanpa Gangguan)

Pastikan `tc qdisc show` bersih dan Laterano `cubic`. Untuk **tiap** berkas (`tes1000.bin`, `tes10000.bin`, `tes5M.bin`): capture baru, curl, Stop, Save.

### Prediksi
> Jumlah segmen naik kira-kira mengikuti ukuran / 1448, tetapi tidak persis proporsional karena overhead tetap (handshake, GET, header HTTP, FIN). Throughput naik jauh lebih cepat daripada proporsional (berkas kecil selesai di slow start). RTT dan ISN tidak bergantung ukuran. Retransmisi 0 di semua berkas karena link sehat.

### Isi tabel E

| No | Sumber |
|---|---|
| 1 | `size=` curl |
| 2 | Statistics > Conversations > TCP, kolom Packets (total kedua arah, termasuk handshake dan ACK). Tulis cara hitungnya |
| 3 | Laterano: I/O Graph `AVG(tcp.analysis.ack_rtt)`, interval besar |
| 4, 5 | `speed_download`, `time_total` curl |
| 6, 7 | `Sequence Number (raw)` pada SYN dan SYN-ACK **capture berkas itu**. ISN server **bukan** ISN client + 1 (angka +1 itu adalah nomor Ack) |
| 8 | Statistics > Endpoints > TCP, Packets Tx Columbia (termasuk SYN dan GET). Lebih tepat: `ip.src == 10.14.3.100 && tcp.flags.ack == 1`, Displayed |
| 9 | Laterano: `tcp.analysis.retransmission`, tulis angka `0` atau jumlahnya |

Kalau tes5M memiliki retransmisi padahal tanpa gangguan: cek `tc qdisc show` (mungkin `netem`/`tbf` belum dilepas). Kalau bersih, kemungkinan buffer overflow di router saat burst (TCP mengirim cepat melebihi kapasitas antrean). Tulis penjelasannya di laporan.

### Pertanyaan E.2 dan jawaban

1. **Proporsional?** Tidak. Segmen naik mengikuti ukuran tetapi ada overhead tetap; berkas besar mendekati ukuran / MSS. Throughput naik jauh lebih cepat dari proporsional karena berkas kecil selesai sebelum cwnd tumbuh dan waktunya didominasi handshake dan request. RTT bisa naik sedikit pada berkas besar karena antrean (dan retransmisi bila ada).
2. **Yang relatif tetap?** RTT dasar jalur (ditentukan propagasi dan pemrosesan router, bukan ukuran), ISN (acak per koneksi), status retransmisi (0 pada link sehat), dan waktu total berkas kecil (didominasi handshake dan RTT).
3. **`time_total` vs RTT?** `time_total` mencakup handshake, request HTTP, seluruh transfer, sampai selesai. RTT hanya waktu satu segmen sampai ACK-nya kembali. Untuk berkas besar `time_total` jauh lebih besar dari RTT karena butuh banyak round trip.

Indikator reliability yang perlu disebut di kesimpulan: retransmisi 0 (atau penjelasannya), ACK kumulatif, ukuran download sama dengan ukuran berkas.

---

## 10. Pertanyaan Lisan yang Mungkin Ditanyakan

**Routing dan jaringan**
- *Apa fungsi `ip_forward`?* Mengizinkan kernel meneruskan paket antar interface sehingga node bertindak sebagai router.
- *Apa itu MASQUERADE?* NAT dinamis: mengganti IP sumber paket internal dengan IP interface keluar.
- *Kenapa perlu static route?* Router tidak tahu subnet yang tidak terhubung langsung; rute diberi tahu manual (`ip route add <subnet> via <next-hop>`).
- *Kenapa TTL 61?* TTL awal 64 dikurangi satu per router yang dilewati; ada 3 router.
- *Kenapa DHCP Discover tidak melewati router?* Broadcast tidak diteruskan router; perlu relay atau server di segmen yang sama.
- *Kenapa Kea harus dijalankan manual?* Container GNS3 tidak memakai systemd.
- *Kenapa Laterano selalu 10.14.4.15?* Reservation di Kea berdasarkan MAC.

**TCP**
- *Tiga flag handshake?* SYN, SYN-ACK, ACK.
- *Kenapa SYN memakai satu nomor urut?* Agar SYN dapat di-ACK (Ack = ISN + 1).
- *Apa itu MSS?* Ukuran maksimum payload per segmen (1460 untuk Ethernet 1500 MTU).
- *Apa itu window scale?* Opsi untuk memperbesar window di atas 65535 (nilai mentah dikali faktor skala).
- *Apa itu SACK?* Penerima memberi tahu blok data yang sudah diterima setelah celah, sehingga hanya segmen hilang yang dikirim ulang.
- *Slow start vs congestion avoidance?* Slow start: cwnd berlipat tiap RTT sampai ssthresh atau loss. Congestion avoidance: cwnd tumbuh lambat (Reno +1 MSS per RTT).
- *Apa itu BDP?* Bandwidth x RTT, jumlah data yang "muat" di jalur.
- *Apa itu head-of-line blocking?* Data setelah celah tidak bisa diserahkan ke aplikasi sebelum segmen yang hilang tiba.
- *Apa itu delayed ACK?* Penerima menunda ACK sebentar atau menggabungkan untuk dua segmen.
- *Apa itu RTO?* Batas waktu menunggu ACK sebelum retransmit; `SRTT + 4 x RTTVAR`, minimum Linux sekitar 200 ms.
- *Kenapa curl `time_total` != RTT?* `time_total` seluruh transaksi, RTT satu round trip.

**tc dan alat**
- *Apa itu netem?* Modul emulasi jaringan (delay, loss, dll). *tbf?* Token bucket filter, membatasi rate.
- *Kenapa gangguan dipasang di dua interface?* Supaya kedua arah (data dan ACK) terkena gangguan.
- *Kenapa `tc qdisc del ... root` penting sebelum skenario baru?* Agar aturan lama tidak bertumpuk dan hasil tidak tercampur.
- *Beda `tcp.window_size` dan `tcp.window_size_value`?* Yang kedua nilai mentah header (belum dikali scale).
- *Kenapa bytes in flight bukan cwnd?* Dipengaruhi juga receive window, bottleneck, dan ketersediaan data.

---

## 11. Koreksi Laporan (temuan dari file laporan yang kamu kirim)

Saya tidak dapat membuka isi screenshot di laporan, jadi daftar ini hanya berdasarkan **teks**. Cek screenshot-nya sendiri.

### Bagian A (A.3 dan A.4)
- Hapus sisa blok `put your answer here (and screenshot)` di A.3.2, A.3.3, A.3.4, A.3.5.
- A.3.4 meminta bukti `kea-dhcp4` berjalan (mis. `ss -ulnp | grep ':67'`) dan `udhcpc -i eth0` serta `ip -br addr` pada Laterano dan Columbia. Pastikan semuanya ada di screenshot.
- A.4.2 no. 8: nilai yang diminta adalah `tcp.analysis.ack_rtt` pada salah satu ACK di Laterano. Tulis nilai yang tertera di *SEQ/ACK analysis* (bukan hitungan manual timestamp), lalu diskusi timer.
- A.4.2 no. 10: jawaban masih umum. Tulis siapa yang mengirim FIN lebih dulu, nomor frame FIN-ACK dan ACK, dan apakah terlihat 4 atau 3 paket (jika tergabung, jelaskan).

### Bagian B
- **B no. 4: `len = 1421842630` salah.** Panjang segmen TCP tidak mungkin sebesar itu (maksimal sekitar 1448-1460). Angka itu kemungkinan nilai field lain (mis. timestamp). Ambil dari baris `TCP Segment Len` (kemungkinan 1448) dan ganti di kedua baris (asli dan retransmisi). Tulis juga nomor frame (4982 dan 4990).
- B no. 3: hanya ada screenshot. Tulis angka `Displayed` jumlah retransmisi.
- B no. 6: belum ada nilai. Tulis RTT terendah dan tertinggi (atau satu nilai sebelum dan sesudah loss), atau jelaskan bahwa pola hanya dibaca dari grafik.
- B no. 2 dan 5 memakai kelompok dupACK 4290, sedangkan no. 7 memakai kelompok 4304. Itu bisa dua kejadian loss berbeda. Tulis eksplisit, atau pakai kejadian yang sama untuk no. 5 dan no. 7 supaya konsisten.
- B no. 8: sudah baik, tapi sebut bahwa label `rto` pada Wireshark tidak berarti timer Linux (lihat bagian 6).
- Tambahkan angka throughput baseline `tes5M` tanpa loss di no. 9 dan 10 dengan screenshot terpisah (sudah ada).

### Bagian C
- **C.2: screenshot Reno belum ada** (`put screenshot here`). Tambahkan screenshot perintah `sysctl` Reno dan curl Reno.
- **C.3 no. 2 dan 3: satuan.** Header kolom "ms", tetapi tertulis detik. Tulis 205 ms dan 203 ms (rata-rata), 400 ms dan 500 ms (maksimum).
- **Kolom "Catatan analisis" berisi salinan instruksi**, bukan analisis. Tulis analisis sendiri per baris (mis. no. 2: RTT kedua algoritma sekitar 200 ms karena delay 100 ms x 2; no. 3: RTT maksimum lebih tinggi karena antrean di tbf; no. 4 dan 5: CUBIC lebih cepat; no. 6 dan 7: perbandingan jumlah).
- No. 4 dan 5: catatan kosong. Isi.
- **No. 9: nilai angka tidak tertulis** di sel CUBIC dan Reno (hanya gambar), dan pada pengerjaan sebelumnya CUBIC dan Reno diukur dengan field berbeda. Pakai **satu field** untuk keduanya dan tulis angka dengan satuan byte, serta catat apakah sudah dikali window scale.
- No. 8: tulis deskripsi pola (awal-akhir) dan kata "perkiraan", bukan hanya gambar.
- **Inkonsistensi:** C.3 no. 1 menyatakan pola gergaji Reno **tidak jelas** di grafik, tetapi jawaban pertanyaan 3 menyatakan Reno "tampak gergaji". Samakan: tulis bahwa secara teori Reno bergerigi, tetapi pada grafik Stevens bentuknya hanya menyerupai karena bukan nilai cwnd langsung.
- Jawaban pertanyaan 2 menyebut CUBIC "mengirim lebih agresif sehingga dupACK lebih banyak" sebagai penyebab. Itu dugaan. Tulis sebagai kemungkinan, bukan kepastian.

### Bagian D
- **D.1 kosong.** Tambahkan screenshot `tc qdisc show` eth1 dan eth2 dengan `loss 5%`.
- **Lost/Total Datagrams hanya `total datagram=432`.** Tulis lengkap: jumlah hilang, total, dan persen (mis. `x/432 (y%)`).
- Jitter: tertulis `0,339 ms di` (kalimat terpotong). Lengkapi.
- Jawaban D.3 no. 1 masih ada `...` placeholder: isi jumlah hilang dan persen.
- Receiver UDP 4,74 dari sender 5 Mbit/s berarti sekitar 5% loss, sesuai netem 5% per arah. Sebut itu.
- Tambahkan baris yang menjelaskan retransmisi TCP (354) dan bahwa receiver TCP sedikit lebih rendah dari sender.
- Tambahkan baseline TCP tanpa loss bila ingin menyatakan TCP "turun".

### Bagian E
- **No. 7 tes1000: `1579359896` adalah ISN client + 1** (angka yang biasanya berupa nomor Ack, bukan sequence number server). ISN server tidak mungkin berbeda persis 1 dari ISN client. Baca ulang `Sequence Number (raw)` pada paket **SYN-ACK** tes1000.
- No. 9 tes1000 dan tes10000: hanya screenshot. Tulis `0 retransmisi (Displayed: 0)`.
- **No. 9 tes5M: 116 retransmisi** padahal skenario tanpa gangguan. Cek `tc qdisc show` dan tulis penjelasan (netem/tbf sisa, atau overflow buffer router saat burst).
- No. 3 tes5M (5,291 ms): kemungkinan ikut dipengaruhi retransmisi dan antrean. Sebut di catatan.
- No. 8: angka (6, 11, 3509) adalah Packets Tx client (termasuk SYN dan GET), bukan murni ACK. Tulis cara hitungnya.
- No. 2: tulis cara hitung (total paket kedua arah, termasuk handshake dan ACK).
- Kesimpulan E.2 perlu menyebut indikator reliability (retransmisi, ACK, ukuran file) sesuai soal.

### Umum
- Typo: "retranmisi" -> "retransmisi".
- Kolom tabel Markdown: satu baris tabel = satu baris teks, Enter diganti `<br>`, tanpa baris kosong, setiap baris diawali dan diakhiri `|`.
- Pisahkan bukti: normal, loss 1%, CUBIC, Reno, TCP, UDP.
- Isi dengan hasil aktual. Hapus semua `put your answer here`.

---

## 12. Troubleshooting

### 12.1 Alur diagnosis

```
ip -br addr
ip route
ping -c 3 <gateway>
ping -c 3 8.8.8.8
ping -c 3 google.com
ss -tlnp
ss -ulnp
```
Uji dari terdekat ke terjauh: gateway -> router tengah -> tujuan -> port aplikasi.

### 12.2 RhodesIsland tidak dapat IP dari NAT / tidak ada internet

1. Node NAT di GNS3 menyala dan link ke eth0 RhodesIsland hijau.
2. `ip link set eth0 up`
3. `udhcpc -i eth0 -v`, lalu `ip -br addr` dan `ip route` (harus ada `default via ...`).
4. Tetap tidak dapat IP: masalah di jaringan NAT/virtual pada GNS3 VM atau host (lihat 12.3).
5. Dapat IP tapi tidak bisa `ping 8.8.8.8`: periksa default route, lalu:
   ```
   sysctl -w net.ipv4.ip_forward=1
   iptables -t nat -A POSTROUTING -j MASQUERADE -s 10.14.0.0/16 -o eth0
   iptables -t nat -L -n -v
   ```
6. Ping IP bisa, nama gagal: `echo "nameserver 8.8.8.8" > /etc/resolv.conf`.
7. Node lain tidak punya internet padahal RhodesIsland punya: cek default gateway node itu, rute dan `ip_forward` di Lungmen dan Ursus.

### 12.3 `virsh` (libvirt) dan jaringan NAT

Berlaku kalau GNS3 VM atau jaringan NAT-nya berjalan lewat libvirt/KVM (jalankan di **host Linux**). Bagian ini saya tulis dari perkiraan; **tambahkan error dan perintah yang benar-benar kamu pakai**. Kalau GNS3 VM di VMware atau VirtualBox, perintah `virsh` tidak berlaku.

```
sudo virsh list --all                 # VM dan statusnya
sudo virsh destroy <nama-vm>          # matikan paksa (bukan menghapus VM)
sudo virsh start <nama-vm>            # nyalakan lagi
sudo virsh net-list --all             # jaringan virtual
sudo virsh net-start default          # nyalakan jaringan default (virbr0)
sudo virsh net-autostart default      # aktif otomatis tiap boot
sudo systemctl restart libvirtd       # restart libvirt
ip -br addr show virbr0               # virbr0 harus UP
```
- `virsh destroy` hanya mematikan paksa. Penghapusan definisi adalah `virsh undefine`; jangan dipakai kecuali yakin.
- Pola: `destroy` -> pastikan jaringan `default` aktif -> `start` -> buka ulang project GNS3.

### 12.4 Error `409 Conflict ... container name already in use`

Container lama tersisa. Di shell **GNS3 VM**:
```
docker ps -a --filter "name=<id-project>"
docker rm -f <id-container>
docker rm -f $(docker ps -aq --filter "name=<id-project>")
```
- `id-project` = UUID pada nama `GNS3.<Node>.<id-project>`.
- Jangan memakai `filter "name=GNS3"` (menghapus project lain juga).
- Di PowerShell Windows, `failed to connect to the docker API ... dockerDesktopLinuxEngine` = Docker Desktop tidak jalan. Kalau GNS3 memakai GNS3 VM, container ada **di dalam VM**.
- Pencegahan: **Stop all nodes**, tutup project, baru shutdown.

### 12.5 Setelah GNS3 dinyalakan ulang

Hilang: IP/rute manual, `sysctl -w`, `iptables`, proses (Kea, server HTTP, iperf3), `tc`, paket `apk add`.
Biasanya aman: topologi, `/etc/...`, `/root/...`.

Urutan:
1. Start semua node.
2. Router: IP, `ip_forward`, rute, NAT (jalankan `/root/setup.sh`).
3. Yan: `apk add kea-dhcp4` bila `command not found`, `mkdir -p /var/lib/kea /run/kea /var/log/kea`, jalankan Kea.
4. Laterano: `udhcpc -i eth0` (harus 10.14.4.15), `apk add python3` bila hilang, `nohup /root/myscripts/start_http.sh > /tmp/http.log 2>&1 &`.
5. Columbia: `ip -br addr`, default route, `ping 10.14.4.15`.
6. Uji curl `tes10000.bin`.

### 12.6 DHCP client tidak dapat IP

- Discover terlihat di `tcpdump` server tapi tidak ada Offer: **Kea tidak berjalan** (`ss -ulnp | grep ':67'` kosong). `tcpdump` melihat paket walau tidak ada proses yang membalas.
- `kea-dhcp4: command not found`: `apk add kea-dhcp4`.
- Kea jalan tapi diam: IP `eth0` server harus di subnet yang dideklarasikan (10.14.4.2/24). Jalankan Kea di foreground untuk melihat log (`no suitable subnet`).
- JSON rusak: `kea-dhcp4 -t /etc/kea/kea-dhcp4.conf`.
- `dhclient: command not found`: pakai `udhcpc -i eth0 -v`.
- Laterano tidak dapat 10.14.4.15: MAC di `reservations` harus sama dengan `ip link show eth0`.

### 12.7 curl gagal

| Gejala | Penyebab | Solusi |
|---|---|---|
| `size=0B` tanpa pesan | `-s` menyembunyikan error | Pakai `-sS` |
| `URL rejected: No host part` | `-o` terlewat atau `[` tersisa | Perintah standar 2.5.1 |
| `Connection refused` | Server HTTP tidak jalan | `ss -tlnp \| grep 9999` di Laterano, jalankan lewat `nohup` |
| `Failed to connect ... after 0 ms` | IP/rute client hilang | `ip -br addr`, `ip route` di Columbia |
| `Connection timed out` | Jalur putus atau `tc` terlalu ketat | Ping, cek rute router, lepas `tc` untuk tes |
| `python3: command not found` | Paket hilang | `apk add python3` (lepas `tc` agar cepat) |

Ping berhasil tidak membuktikan port 9999 terbuka. `Connection refused` berarti paket sampai tetapi tidak ada proses yang listen.

### 12.8 tc

- `tc qdisc show dev eth1` dan `eth2` untuk cek.
- `tc qdisc del dev ethX root` menghapus semua (termasuk child).
- `Exclusivity flag on`: sudah ada qdisc, `del` dulu. `Cannot delete qdisc with handle of zero`: tidak ada aturan.
- Pasang `tc` membuat `apk`/`apt` sangat lambat: instal paket **sebelum** gangguan.
- Wajib lepas `tc` sebelum mengukur baseline atau berpindah bagian.

### 12.9 Wireshark

| Masalah | Solusi |
|---|---|
| Handshake tidak terlihat | Capture dimulai setelah curl, atau daftar terscroll (`Ctrl+Home`) |
| Hasil capture hilang | Jendela ditutup tanpa Save |
| Tidak ada paket | Link salah di-capture, hapus filter sementara |
| Filter kosong | Belum Enter, field salah, atau capture salah (Columbia vs Laterano) |
| Dua koneksi dalam satu capture | Tambahkan `tcp.port == <port klien>` |
| Seq selalu 0 | Relative numbers aktif, baca `Sequence Number (raw)` |
| Info terpotong | Tambahkan kolom Ack/Seq/Len, lebarkan kolom |
| I/O Graph skala aneh | Hapus grafik sisa, periksa AVG (bukan SUM), Y Field, filter, interval |
| Sirip hiu (restart) tidak jalan | Stop dan Start capture lewat GNS3 |

### 12.10 Salah ketik umum

- Perintah terpotong ke dua baris (mis. `mkdir -p a b` lalu `c` di baris terpisah).
- `curl` tanpa `-o`.
- Mengetik di terminal yang sedang menjalankan server (`Ctrl+C` mematikannya). Pakai terminal kedua, `nohup`, atau tmux.
- Placeholder `[IP Prefix]` atau `<angka>` tidak diganti.
- Tabel Markdown rusak (lihat bagian 11).

---

## 13. Checklist Bukti

- Screenshot topologi GNS3 dan hasil konektivitas (ping, `ip route`, lease DHCP).
- Command konfigurasi penting dan tabel route, bukan hanya hasil ping.
- Screenshot Wireshark dengan filter dan status bar terlihat.
- Pisahkan data: normal, loss 1%, CUBIC, Reno, TCP, UDP.
- Indikator probabilistik yang tidak muncul (mis. timeout retransmission): jelaskan apa yang diamati dan alasannya.
- Prediksi ditulis **sebelum** melihat hasil (A.4.1, B.1, C.1).
- Isi dengan hasil aktual, bukan perkiraan.
- 
### 1.4 Referensi

- https://www.geeksforgeeks.org/computer-networks/open-systems-interconnection-model-osi/

- https://www.tutorialspoint.com/data_communication_computer_network/computer_network_models.htm

- https://www.geeksforgeeks.org/computer-networks/computer-network-models/

- https://www.lifewire.com/layers-of-the-osi-model-illustrated-818017

1. [Web Server](Web%20server/README.md)
2. [DNS (Domain Name System)](DNS/README.md)
3. [Reverse Proxy](Reverse%20Proxy/README.md)
