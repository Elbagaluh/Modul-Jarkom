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
# Modul 3: Reliable Data Transfer TCP & Static Routing (Panduan Praktikum)

Isi: tutorial pra-praktikum, daftar filter Wireshark beserta hasil yang seharusnya, kemungkinan pertanyaan praktikum, dan troubleshooting.
Prefix IP yang dipakai: **10.14**. Nilai contoh (ISN, RTT, throughput) berasal dari lab sendiri dan **akan berbeda** di percobaan baru. Pahami caranya, jangan hafal angkanya.

---

## 1. Peta Topologi

| Node | Peran | IP |
|---|---|---|
| RhodesIsland | Router ke NAT + titik gangguan `tc` | eth0 DHCP (NAT), eth1 10.14.1.1/24, eth2 10.14.2.1/24 |
| Lungmen | Router | eth0 10.14.1.2/24 (gw 10.14.1.1), eth1 10.14.3.1/24 |
| Ursus | Router | eth0 10.14.2.2/24 (gw 10.14.2.1), eth1 10.14.4.1/24 |
| Victoria | DHCP server subnet 3.0/24 | eth0 10.14.3.2/24, pool 10.14.3.100-120 |
| Yan | DHCP server subnet 4.0/24 | eth0 10.14.4.2/24, pool 10.14.4.50-60 |
| Laterano | Web server, sender, capture sisi server | 10.14.4.15 (reservation dari Yan) |
| Columbia | Client (curl, iperf3), capture sisi client | 10.14.3.100 (DHCP Victoria) |

Jalur data: Columbia -> Switch1 -> Lungmen -> RhodesIsland -> Ursus -> Hub1 -> Laterano. Antara Columbia dan Laterano ada **3 router**, jadi TTL turun dari 64 menjadi **61** (bisa dipakai sebagai bukti).

---

## 2. Tutorial Persiapan (Pra-Praktikum)

### 2.1 IP statis router (`/etc/network/interfaces`)

```
# RhodesIsland
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

# Lungmen
auto eth0
iface eth0 inet static
    address 10.14.1.2
    netmask 255.255.255.0
    gateway 10.14.1.1
auto eth1
iface eth1 inet static
    address 10.14.3.1
    netmask 255.255.255.0

# Ursus
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

### 2.2 Static routing + NAT

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

Catatan:
- Error `File exists` pada `ip route add` aman diabaikan (rute sudah ada).
- Error `Nexthop has invalid gateway` berarti IP gateway **tidak satu subnet** dengan IP interface yang dipakai. Cek `ip -br addr` (lihat Troubleshooting).
- Perintah `ip route add`, `ip addr add`, `sysctl -w` **tidak permanen**. Simpan di `init.sh` atau script `/root/setup.sh` per node.

### 2.3 DNS di semua node

```
echo "nameserver 8.8.8.8" > /etc/resolv.conf
ping -c 3 google.com
```

### 2.4 DHCP dengan Kea

Pasang kalau belum ada (butuh internet): `apk add kea-dhcp4` (Alpine) atau `apt install -y kea-dhcp4-server` (Debian/Ubuntu).

Isi `/etc/kea/kea-dhcp4.conf` di **Yan**:

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

Victoria sama, dengan subnet `10.14.3.0/24`, pool `10.14.3.100 - 10.14.3.120`, router `10.14.3.1`, tanpa reservation.

Cara menjalankan (container GNS3 **tidak punya systemd**, Kea harus dijalankan manual):

```
mkdir -p /var/lib/kea /run/kea /var/log/kea
kea-dhcp4 -t /etc/kea/kea-dhcp4.conf     # tes sintaks, harus tanpa error
kea-dhcp4 -c /etc/kea/kea-dhcp4.conf &   # jalankan
ss -ulnp | grep ':67'                    # port 67 harus muncul
```

Cara dapat MAC Laterano: `ip link show eth0` di Laterano (baris `link/ether`).

Client:
```
udhcpc -i eth0 -v      # Alpine / BusyBox
```
Hasil yang benar: `lease of 10.14.4.15 obtained from 10.14.4.2`.

### 2.5 Web server di Laterano

```
apk update && apk add python3
python3 --version
mkdir -p /root/myscripts/myweb
cat > /root/myscripts/start_http.sh << 'EOF'
#!/bin/sh
mkdir -p /root/myscripts/myweb
cd /root/myscripts/myweb
python3 -m http.server 9999 -d /root/myscripts/myweb
EOF
chmod +x /root/myscripts/start_http.sh
```

Berkas uji (jalankan di `/root/myscripts/myweb`, ini perintah terminal, **bukan** isi file):

```
cd /root/myscripts/myweb
dd if=/dev/random of=tes1000.bin   bs=1    count=1000
dd if=/dev/random of=tes10000.bin  bs=10   count=1000
dd if=/dev/random of=tes50000.bin  bs=50   count=1000
dd if=/dev/random of=tes5M.bin     bs=5000 count=1000
ls -l
```
Kalau `/dev/random` macet (kekurangan entropi), ganti dengan `/dev/urandom`.

Jalankan server supaya tidak mati saat terminal ditutup:
```
nohup /root/myscripts/start_http.sh > /tmp/http.log 2>&1 &
ss -tlnp | grep 9999        # harus ada LISTEN
```
Atau pakai `tmux` (split: `Ctrl+b` lalu `%` kiri/kanan, `"` atas/bawah, panah untuk pindah pane).

Salah ketik umum: `mkdir start_http.sh` membuat **direktori**. Hapus dengan `rmdir /root/myscripts/start_http.sh`.

### 2.6 Verifikasi awal dari Columbia

```
ip -br addr                       # eth0 dapat 10.14.3.x
ping -c 3 10.14.4.15
curl -sS -o /dev/null -w "size=%{size_download}B speed=%{speed_download}B/s total=%{time_total}s\n" http://10.14.4.15:9999/tes10000.bin
```
Browser: `http://10.14.4.15:9999` menampilkan listing keempat berkas.

### 2.7 Perintah curl standar

```
curl -sS -o /dev/null -w "size=%{size_download}B speed=%{speed_download}B/s total=%{time_total}s\n" http://10.14.4.15:9999/<berkas>
```
- `-o /dev/null` buang isi, `-s` senyap, `-S` tetap tampilkan error, `-w` cetak ringkasan.
- **`-o` jangan terlewat.** Tanpa `-o`, `/dev/null` dibaca sebagai URL (`URL rejected: No host part`).
- Jangan menulis `[IP Prefix]` literal atau tanda `[`: itu notasi IPv6 di URL.
- `size=0B` berarti gagal connect. Pakai `-sS` untuk melihat penyebabnya.

---

## 3. Cara Capture Wireshark di GNS3

1. Klik kanan **link** (Columbia-Switch1 untuk sisi client, Hub1-Laterano untuk sisi server) -> **Start capture**.
2. Terapkan filter `tcp.port == 9999`, lalu tekan **Enter** (kolom hijau = valid).
3. **Mulai capture sebelum menjalankan curl.** Satu capture sebaiknya hanya berisi satu download.
4. Setelah curl selesai: Stop (`Ctrl+E`), lalu Save (`Ctrl+S`). Capture dari GNS3 lewat *standard input*, **hilang kalau jendela ditutup tanpa disimpan**. Judul jendela bertanda `*` berarti belum disimpan.
5. Mulai capture bersih: di GNS3 klik kanan link -> Stop capture, lalu Start capture lagi.

Pengaturan yang membantu:
- Edit > Preferences > Protocols > TCP: matikan *Relative sequence numbers* untuk melihat nilai asli. Atau baca baris `Sequence Number (raw)`.
- Tambahkan kolom: klik field di panel detail -> klik kanan -> **Apply as Column** (Seq, Ack, Len, `The RTT to ACK the segment was`, Window).
- Status bar (paling bawah): `Displayed: N` = jumlah paket yang lolos filter. **Wajib ikut di-screenshot** sebagai bukti jumlah.
- Panel detail kecil? Seret garis pemisah daftar paket dan panel detail ke atas.

Pembagian capture (sesuai catatan di soal):
- **A:** nomor 1-8 capture Laterano, 9-10 capture Columbia.
- **B:** nomor 2 dan 5 capture Columbia, selain itu Laterano.
- **C:** nomor 4, 5, 9 Columbia (4 dan 5 dari output curl), selain itu Laterano.

---

## 4. Daftar Filter Wireshark dan Hasil yang Seharusnya

| Tujuan | Filter | Hasil yang diharapkan |
|---|---|---|
| Hanya koneksi uji | `tcp.port == 9999` | Semua paket download |
| Satu koneksi spesifik | `tcp.port == 40454` (port klien dari kolom Info) | Satu download saja |
| Handshake | `tcp.flags.syn == 1` | SYN dan SYN-ACK |
| Arah server ke client | `ip.src == 10.14.4.15 && tcp.len > 0` | Segmen data |
| Arah client ke server | `ip.src == 10.14.3.100` | GET dan ACK |
| Retransmisi (semua) | `tcp.analysis.retransmission` | A: 0. B/C: > 0 |
| Fast retransmission | `tcp.analysis.fast_retransmission` | Label `[TCP Fast Retransmission]` |
| Duplicate ACK | `tcp.analysis.duplicate_ack` | `[TCP Dup ACK nnnn#1]`, `#2`, `#3` ... |
| RTT | `tcp.analysis.ack_rtt` | ACK yang punya data RTT |
| Bytes in flight | `tcp.analysis.bytes_in_flight` | Proksi cwnd |
| Segmen hilang tak tertangkap | `tcp.analysis.lost_segment` | `Previous segment not captured` |
| FIN | `tcp.flags.fin == 1` | Penutupan koneksi |
| Window receiver | `ip.src == 10.14.3.100 && tcp.port == 9999` | Untuk I/O Graph window |

Catatan filter:
- Jangan menulis placeholder `<angka>` dengan tanda `<>` di filter. Tulis angkanya saja, misalnya `tcp.seq == 851631`.
- Filter hanya dihitung hijau jika valid. Merah = salah sintaks.
- Nilai `tcp.seq` di filter mengikuti mode tampilan (relatif atau raw). Konsisten.

---

## 5. Bagian A: Transfer Normal (Skenario 1)

**Persiapan:** `tc qdisc show dev eth1` dan `eth2` harus bersih (tanpa `netem` atau `tbf`). Kalau ada: `tc qdisc del dev eth1 root` dan `tc qdisc del dev eth2 root`. Error `Cannot delete qdisc with handle of zero` berarti memang tidak ada aturan, aman.

**Prediksi ISN:** bukan 0, melainkan angka acak 32-bit.

| No | Parameter | Cara baca di Wireshark | Hasil seharusnya |
|---|---|---|---|
| 1 | IP sumber/tujuan | Paket SYN, bagian IP | Src 10.14.3.100 (Columbia), Dst 10.14.4.15 (Laterano), port tujuan 9999, port sumber acak (ephemeral) |
| 2 | Handshake | 3 paket pertama | SYN (client), SYN-ACK (server), ACK (client) |
| 3 | ISN client | `Sequence Number (raw)` pada SYN | Angka besar acak (contoh 3308525059). Tampilan `Seq=0` hanyalah relative number |
| 4 | ISN server | `Sequence Number (raw)` pada SYN-ACK | Beda dari ISN client (contoh 3145121483) |
| 5 | ACK setelah handshake | Ack pada SYN-ACK dan ACK ketiga | SYN-ACK: Ack = ISN client + 1. ACK ketiga: Ack = ISN server + 1 |
| 6 | SEQ berikutnya | Dua segmen data berurutan | seq berikutnya = seq + len (contoh 3101 + 1448 = 4549) |
| 7 | Pola ACK | ACK dari client | Naik kumulatif, tanpa duplikat (contoh 205, 1653, 3101, 4549 ...) |
| 8 | RTT | `tcp.analysis.ack_rtt` di Laterano | Beberapa ms (jalur stabil) |
| 9 | Retransmisi | `tcp.analysis.retransmission` di Columbia | 0 |
| 10 | Penutupan | `tcp.flags.fin == 1` | FIN-ACK, ACK, FIN-ACK, ACK (kadang digabung jadi 3 paket) |

Rumus yang sering ditanyakan:
- Handshake: SYN memakai 1 nomor urut, jadi Ack = ISN + 1.
- Respons HTTP: GET 91 byte -> server membalas Ack = 1 + 91 = 92 (relatif).
- Ukuran file: 10000 B dengan header HTTP 204 B -> ACK akhir relatif = 1 + 204 + 10000 = 10205 (kira-kira).
- MSS 1460, payload umum 1448 B (karena opsi timestamp). Jumlah segmen data tes5M kira-kira 5.000.000 / 1448 ≈ 3450.

Cara membaca ISN dari hex (kalau diminta): pada byte setelah port tujuan (4 byte) adalah sequence number. Contoh `c5 34 16 03` = 0xC5341603 = 3308525059.

### Pertanyaan A.4.2 yang mungkin keluar

1. **Fungsi seq/ack?** Seq memberi nomor urut tiap byte sehingga penerima bisa mengurutkan, mendeteksi hilang/duplikat. Ack menyatakan byte berikutnya yang diharapkan sekaligus konfirmasi semua byte sebelumnya diterima.
2. **Kenapa ACK kumulatif?** Efisien (satu ACK untuk banyak segmen), tahan terhadap ACK yang hilang (ACK berikutnya mencakup yang sebelumnya), sederhana. Kekurangannya tidak memberi tahu segmen setelah celah; itu dilengkapi opsi SACK.
3. **Segmen hilang?** Penerima mengirim dupACK; pengirim fast retransmit setelah 3 dupACK atau retransmit saat RTO habis; cwnd dikecilkan; setelah segmen tiba, ACK kumulatif melompat.
4. **Bukti reliable?** Handshake sukses, seq berurutan, ACK kumulatif naik, retransmisi 0, curl `size` sama dengan ukuran file, koneksi ditutup FIN/ACK.
5. **Kenapa ISN acak?** Mencegah serangan prediksi/spoofing sequence number dan mencegah segmen lama dari koneksi sebelumnya tercampur (RFC 6528).
6. **Timer retransmisi (RTO) terlalu pendek/panjang?** Pendek: timeout sebelum ACK tiba, retransmisi tidak perlu (spurious), bandwidth terbuang, cwnd turun. Panjang: saat segmen benar-benar hilang pengirim menunggu lama, link menganggur, waktu transfer membengkak. TCP menghitung RTO adaptif: SRTT + 4×RTTVAR.

---

## 6. Bagian B: Packet Loss 1% (Skenario 2)

```
tc qdisc del dev eth1 root
tc qdisc del dev eth2 root
tc qdisc add dev eth1 root netem loss 1%
tc qdisc add dev eth2 root netem loss 1%
tc qdisc show dev eth1
tc qdisc show dev eth2
```
Output harus memuat `qdisc netem ... loss 1%`. Error `Exclusivity flag on` berarti sudah ada qdisc: `del` dulu.

Download `tes5M.bin`, dengan capture sudah berjalan.

**Prediksi:** penerima tidak mengirim NAK, melainkan duplicate ACK (nomor Ack sama berulang). Setelah 3 dupACK, pengirim fast retransmit. Throughput turun, waktu naik karena cwnd dikecilkan.

| No | Filter / cara | Hasil seharusnya |
|---|---|---|
| 1 | `tc qdisc show` | `loss 1%` di eth1 dan eth2 |
| 2 (Columbia) | `tcp.analysis.duplicate_ack` | Kelompok dupACK dengan Ack identik, tanpa paket NAK |
| 3 (Laterano) | `tcp.analysis.retransmission`, lihat Displayed | > 0 (contoh 40-an) |
| 4 (Laterano) | Klik satu retransmisi, catat seq dan len, lalu `tcp.seq == <seq>` | Dua paket (atau lebih) dengan seq dan len sama: asli (waktu lebih awal) dan retransmisi |
| 5 (Columbia) | `tcp.analysis.duplicate_ack`, satu kelompok `#1`..`#3`+ | Minimal 3 dupACK berturut-turut |
| 6 (Laterano) | Kolom RTT, atau Statistics > TCP Stream Graphs > Round Trip Time | RTT cenderung naik di sekitar loss (tulis apa adanya jika tidak jelas) |
| 7 (Laterano) | `tcp.analysis.fast_retransmission` + dupACK ke-3 | Selisih dupACK#3 ke retransmisi sekitar 1 ms, jauh di bawah RTO minimum Linux (±200 ms) |
| 8 (Laterano) | Retransmisi dengan jeda ≥ 200 ms dari paket asli | Kalau tidak ada: semua loss dipulihkan lewat dupACK |
| 9 | curl `speed_download` | Lebih rendah dari baseline `tes5M` tanpa loss |
| 10 | curl `time_total` | Lebih lama dari baseline |
| 11 | I/O Graph `MAX(tcp.analysis.bytes_in_flight)`, filter `ip.src == 10.14.4.15 && tcp.port == 40454`, interval 10 ms | Naik, turun tajam setelah loss, naik lagi (pola gergaji) |

**Jebakan penting:**
- Angka di `[TCP Dup ACK 1927#1]`: **1927 adalah nomor frame ACK asli**, bukan nomor Ack. `#1`, `#2` adalah urutan duplikat. Untuk melihat nomor Ack, tambahkan kolom *Acknowledgment Number*.
- Baseline untuk perbandingan harus `tes5M.bin` **tanpa loss** (bukan `tes10000.bin`), karena throughput bergantung ukuran file. Contoh hasil lab: tanpa loss 18.320.588 B/s, 0,273 s; loss 1%: 8.790.513 B/s, 0,569 s.
- `tcp.analysis.rto` adalah nama field yang menyesatkan. Nilai "RTO for this segment" di *SEQ/ACK analysis* hanya selisih waktu dari segmen asli ke retransmisi, **bukan** timer RTO Linux. Retransmisi berlabel non-fast dengan jeda < 1 ms bukan timeout; itu retransmisi saat fase recovery (SACK).
- Timeout retransmission mungkin tidak muncul; itu valid. Jelaskan: dupACK selalu cukup sebelum timer habis.
- dupACK yang melebihi `#3` (misal `#29`) wajar: segmen lama masih berdatangan di luar urutan.
- Loss dipasang di dua interface, jadi dupACK dari Columbia juga bisa hilang sebelum sampai Laterano. Jumlah dupACK di Columbia dan Laterano bisa berbeda.
- `bytes_in_flight` adalah **proksi**, bukan pengukuran cwnd langsung. Tulis "perkiraan".

### Pertanyaan B.3

1. **TCP tahu segmen hilang?** Lewat duplicate ACK (3x) dan lewat timeout RTO. TCP tidak memakai NAK.
2. **Fast retransmit vs timeout?** Fast retransmit dipicu 3 dupACK, retransmisi cepat (sekitar 1 RTT), cwnd turun sekitar setengah. Timeout menunggu RTO (min ±200 ms), dipakai saat dupACK tidak cukup, dan cwnd turun ke 1 MSS (slow start).
3. **Kenapa throughput turun?** Loss dianggap congestion, cwnd dikecilkan, ada waktu recovery, dan data setelah celah tertahan sampai segmen hilang tiba (head-of-line blocking).
4. **Bukti reliable?** Retransmisi punya seq dan len sama dengan aslinya, ACK kumulatif melompat setelah retransmisi, curl tetap `size=5000000B`, hanya waktu yang bertambah.

---

## 7. Bagian C: CUBIC vs Reno (Skenario 3)

Pasang gangguan (sesuai modul: tbf root, netem child):

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
Hasil `tc qdisc show`: `tbf ... rate 2Mbit` dan `netem ... delay 100ms loss 1%`. Cek dengan `ping -c 5 10.14.4.15` dari Columbia: RTT sekitar **200 ms** (100 ms × 2 arah).

Algoritma di **Laterano (sender)**:

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
Setting berlaku untuk koneksi **baru**, server HTTP tidak perlu restart. Jalankan sysctl di terminal Laterano kedua kalau terminal pertama dipakai server.

**Prediksi kurva:**
- Reno: cwnd tumbuh linear (+1 MSS per RTT), turun separuh saat loss, pola gergaji.
- CUBIC: cwnd turun lebih kecil (faktor 0,7), tumbuh kubik (cepat, melambat dekat W_max, lalu cepat lagi), pulih lebih cepat.

Transfer lama (puluhan detik) karena 2 Mbit/s = maksimum teoritis 250.000 B/s. Tunggu sampai selesai.

**Baris tabel C dan cara mengambilnya** (pakai filter, fungsi, dan interval yang **sama** untuk CUBIC dan Reno, satu capture per algoritma):

| No | Cara |
|---|---|
| 1 | Klik paket data server (`10.14.4.15 -> 10.14.3.100`), lalu Statistics > TCP Stream Graphs > Time Sequence (Stevens). Bandingkan bentuk dengan prediksi, jelaskan bila beda |
| 2, 3 | Laterano: Statistics > I/O Graphs, filter `tcp.port == 9999 && tcp.analysis.ack_rtt`, Y Field `tcp.analysis.ack_rtt`, **AVG** (rata-rata) dan **MAX** (maksimum), interval besar (1 min atau 10 min) agar satu titik. Klik titik, baca nilai di teks bawah grafik (`Click to select packet ... = ...`). Satuan detik, kalikan 1000 untuk ms |
| 4, 5 | Output curl di Columbia (`speed`, `total`) |
| 6 | Laterano: `tcp.analysis.retransmission`, Displayed |
| 7 | Columbia: `tcp.analysis.duplicate_ack`, Displayed |
| 8 | Laterano: I/O Graph `MAX(tcp.analysis.bytes_in_flight)`, filter `ip.src == 10.14.4.15 && tcp.port == 9999` (perkiraan) |
| 9 | Columbia: I/O Graph `AVG(tcp.window_size)` atau `tcp.window_size_value`, filter `ip.src == 10.14.3.100 && tcp.port == 9999`. **Pilih satu field dan konsisten** |
| 10 | Statistics > TCP Stream Graphs > Throughput, deskripsikan konstan atau berfluktuasi |

Hal penting:
- `tcp.window_size_value` = nilai mentah di header (maks 65535, belum dikali window scale). `tcp.window_size` = nilai sebenarnya (sudah diskalakan). Tulis di catatan field mana yang dipakai.
- Receive window ditentukan **sisi penerima (Columbia)**, bukan algoritma sender. Beda nilai CUBIC vs Reno adalah efek auto-tuning buffer.
- Rata-rata RTT keduanya ≈ 200 ms (dari delay), jadi beda algoritma terlihat di kecepatan pemulihan, bukan di rata-rata RTT.
- Di I/O Graph, hapus grafik sisa (tombol **-**) supaya skala dan legenda tidak membingungkan. Kalau titik terpecah per menit, naikkan interval ke 10 min.
- **Jangan klaim mengukur cwnd langsung** dari grafik Stevens atau `bytes_in_flight`. Pakai kata "menyerupai" atau "perkiraan".
- Loss acak. Satu percobaan bisa menyesatkan; ulangi 2-3 kali jika selisih kecil dan sebutkan batasan ini.

Contoh hasil lab (hanya gambaran): CUBIC 124.358 B/s, 40,2 s, retransmisi 24; Reno 79.578 B/s, 62,7 s, retransmisi 41. Keduanya di bawah 250.000 B/s.

### Pertanyaan C.3

1. **Throughput lebih tinggi?** Umumnya CUBIC: cwnd turun lebih kecil (30%) dan tumbuh berdasarkan waktu sejak loss, pulih cepat. Reno turun 50% dan +1 MSS per RTT, lambat di RTT 200 ms. Tulis sesuai hasilmu.
2. **Hubungan retransmisi dan algoritma?** Keduanya loss-based, retransmisi dipicu loss netem. Jumlah dipengaruhi lamanya transfer dan agresivitas; loss acak sehingga perlu pengulangan untuk kesimpulan kuat.
3. **Bentuk kurva?** Reno linear bergerigi; CUBIC kubik (cekung lalu cembung di sekitar titik loss sebelumnya). Dengan bottleneck tbf, perbedaan bisa tipis.
4. **Paling sensitif RTT tinggi?** Reno (pertumbuhan per-RTT). CUBIC bergantung waktu real sejak loss.
5. **Lebih efisien di latency tinggi?** Biasanya CUBIC, dengan alasan yang sama.

---

## 8. Bagian D: TCP vs UDP (Loss 5%)

```
tc qdisc del dev eth1 root
tc qdisc del dev eth2 root
tc qdisc add dev eth1 root netem loss 5%
tc qdisc add dev eth2 root netem loss 5%
tc qdisc show dev eth1
tc qdisc show dev eth2
```

Pasang iperf3 dulu **saat jaringan bersih** (`apk add iperf3`) di Laterano dan Columbia.

```
# Laterano
iperf3 -s
# Columbia
iperf3 -c 10.14.4.15 -t 10
iperf3 -c 10.14.4.15 -u -b 5M -t 10
```

Cara baca ringkasan iperf3:

| Sel tabel | Ambil dari |
|---|---|
| Bitrate sender / receiver | Dua baris terakhir berlabel `sender` dan `receiver` |
| Retransmission (TCP) | Kolom `Retr` baris sender |
| Lost/Total Datagrams (UDP) | Kolom di baris ringkasan UDP, mis. `12/432 (2.8%)` |
| Jitter (UDP) | Kolom `Jitter` (ms) |

Hasil seharusnya:
- TCP: ada `Retr` > 0, throughput turun dari kapasitas link.
- UDP: sender tetap sekitar 5 Mbit/s; receiver sedikit lebih rendah (sesuai loss); ada Lost/Total dan jitter.
- Bandingkan angka yang setara: sender dengan sender, receiver dengan receiver. TCP dan UDP punya batas berbeda (UDP dibatasi `-b 5M`, TCP tidak), jadi jangan langsung membandingkan angka TCP dengan 5 Mbit/s tanpa menyebut itu.

### Pertanyaan D.3

1. **Kenapa UDP menampilkan persentase loss, TCP tidak?** iperf3 menyisipkan nomor urut di payload UDP, receiver menghitung celahnya. Di TCP, loss ditutup retransmisi di kernel sehingga aplikasi tidak melihat loss; iperf3 hanya melaporkan jumlah `Retr`.
2. **Kenapa throughput UDP tetap dekat `-b`?** UDP tidak punya congestion control dan retransmisi; sender mengirim sesuai target. Loss hanya terlihat di receiver.
3. **Kenapa TCP turun?** Loss dianggap congestion, cwnd dikecilkan, ada waktu recovery. UDP tidak memantau loss.
4. **Aplikasi cocok UDP?** Yang mementingkan latensi rendah dan menoleransi loss: VoIP, video call, live streaming, game online, DNS. Data terlambat tidak berguna, retransmisi TCP menambah delay.

---

## 9. Bagian E: Tiga Ukuran Berkas (Tanpa Gangguan)

Pastikan `tc qdisc show` bersih dan algoritma Laterano `cubic`. Untuk **tiap** berkas (`tes1000.bin`, `tes10000.bin`, `tes5M.bin`): capture baru, curl, stop, simpan.

| No | Sumber |
|---|---|
| 1 | `size=` dari curl |
| 2 | Statistics > Conversations > TCP, kolom **Packets** (total dua arah, termasuk handshake dan ACK). Sebut cara hitungnya |
| 3 | Laterano: I/O Graph `AVG(tcp.analysis.ack_rtt)`, interval besar |
| 4, 5 | `speed_download`, `time_total` dari curl |
| 6, 7 | ISN dari `Sequence Number (raw)` pada SYN dan SYN-ACK **capture berkas itu** (jangan salin dari A) |
| 8 | Statistics > Endpoints > TCP, baris Columbia, Packets Tx. Catatan: itu semua paket client (termasuk SYN dan GET). Lebih tepat `ip.src == 10.14.3.100 && tcp.flags.ack == 1` |
| 9 | Laterano: `tcp.analysis.retransmission`, seharusnya Displayed: 0 |

Contoh hasil lab: tes1000 13 segmen, tes10000 22 segmen, tes5M 4036 segmen; throughput 145.053, 1.769.911, 18.436.374 B/s.

**Kalau tes5M punya retransmisi (mis. 116) padahal tanpa gangguan:** cek dulu `tc qdisc show` (mungkin `netem`/`tbf` belum dilepas). Kalau bersih, kemungkinan buffer overflow di router saat burst 5 MB (TCP agresif mengirim cepat melebihi kapasitas antrean). Tulis penjelasan itu, bukan menyembunyikannya.

### Pertanyaan E.2

1. **Proporsional?** Tidak. Segmen naik mengikuti ukuran tapi ada overhead tetap (handshake, GET, FIN), jadi berkas kecil tidak proporsional; berkas besar mendekati ukuran/MSS. Throughput naik jauh lebih cepat dari proporsional karena berkas kecil selesai di fase slow start dan waktunya didominasi handshake. RTT bisa naik sedikit pada berkas besar karena antrean.
2. **Relatif tetap?** RTT dasar jalur (propagasi), ISN (acak, tidak terkait ukuran), status retransmisi (0 bila link sehat).
3. **`time_total` vs RTT?** `time_total` mencakup handshake, request, seluruh transfer, dan penutupan; RTT hanya waktu satu segmen sampai ACK-nya kembali (satu round trip).

---

## 10. Troubleshooting

### 10.1 Cara cepat mendiagnosis

```
ip -br addr          # IP dan status interface
ip route             # default route dan rute antar subnet
ping -c 3 <gateway>  # jangkauan tetangga
ping -c 3 8.8.8.8    # internet (IP)
ping -c 3 google.com # DNS
ss -tlnp             # port yang listen (server)
ss -ulnp             # port UDP (DHCP port 67)
```
Prinsip: uji dari yang terdekat ke terjauh. Gateway -> router tengah -> tujuan -> port aplikasi.

### 10.2 RhodesIsland (router NAT) tidak dapat IP / tidak ada internet

RhodesIsland eth0 harus DHCP dari NAT node. Cek berurutan:

1. **Node NAT sudah terhubung dan menyala di GNS3?** Link dari NAT ke eth0 RhodesIsland harus hijau.
2. **Interface up?** `ip link set eth0 up`
3. **Minta DHCP manual:** `udhcpc -i eth0 -v` (Alpine) atau `dhclient -v eth0` (kalau ada). Lalu `ip -br addr` dan `ip route` (harus ada `default via ...`).
4. **Tetap tidak dapat IP?** Masalahnya biasanya di sisi NAT/virtual network pada GNS3 VM atau host (lihat 10.3).
5. **Dapat IP tapi tidak bisa ping 8.8.8.8:** cek `ip route` (default route), lalu forwarding dan NAT:
   ```
   sysctl -w net.ipv4.ip_forward=1
   iptables -t nat -A POSTROUTING -j MASQUERADE -s 10.14.0.0/16 -o eth0
   iptables -t nat -L -n -v
   ```
6. **Ping IP bisa, nama gagal:** `echo "nameserver 8.8.8.8" > /etc/resolv.conf`
7. Node lain (Yan, Laterano, Columbia) tidak punya internet padahal RhodesIsland punya: cek **rute di router tengah**, default gateway node itu, dan `ip_forward` di Lungmen dan Ursus. Ingat `apk update` / `apk add` butuh internet.

Catatan: node NAT di GNS3 (Linux) biasanya memakai jaringan virtual `virbr0` dari libvirt. Kalau `virbr0` tidak aktif, node yang tersambung ke NAT tidak mendapat IP.

### 10.3 `virsh` (libvirt / VM GNS3) bermasalah

Catatan: bagian ini berlaku jika GNS3 VM atau jaringan NAT-nya berjalan lewat libvirt/KVM. Perintah di bawah dijalankan di mesin host Linux, bukan di dalam container. Sesuaikan nama VM dengan hasil `virsh list --all`. **Tambahkan error asli yang kamu alami di sini supaya README ini sesuai setupmu.**

```
sudo virsh list --all                 # lihat VM dan statusnya
sudo virsh destroy <nama-vm>          # matikan paksa (setara cabut listrik, bukan hapus VM)
sudo virsh start <nama-vm>            # nyalakan lagi
sudo virsh net-list --all             # lihat jaringan virtual
sudo virsh net-start default          # nyalakan jaringan default (virbr0)
sudo virsh net-autostart default      # supaya otomatis aktif tiap boot
sudo systemctl restart libvirtd       # restart layanan libvirt
ip -br addr show virbr0               # virbr0 harus UP dengan IP 192.168.122.1
```

- `virsh destroy` **tidak menghapus** VM, hanya mematikan paksa. Penghapusan definisi adalah `virsh undefine` (jangan dipakai kecuali yakin).
- Pola yang membantu: matikan VM (`destroy`) -> pastikan jaringan `default` aktif -> nyalakan VM (`start`) -> buka ulang project GNS3.
- Kalau GNS3 VM berjalan di VMware/VirtualBox (bukan libvirt), perintah `virsh` tidak berlaku. Restart VM lewat VMware/VirtualBox.

### 10.4 Error `409 Conflict ... container name already in use`

Container lama dengan nama sama tersisa (biasanya GNS3 tidak ditutup dengan bersih). Jalankan di shell **GNS3 VM** (SSH ke VM, atau konsol VM -> Shell):

```
docker ps -a --filter "name=<id-project>"
docker rm -f <id-container>
# hapus semua container project ini:
docker rm -f $(docker ps -aq --filter "name=<id-project>")
```
- `id-project` adalah bagian UUID di nama container (`GNS3.<Node>.<id-project>`).
- Jangan memakai `filter "name=GNS3"` untuk menghapus semuanya, itu menghapus container project lain juga.
- Kalau `docker` dijalankan di PowerShell Windows dan muncul `failed to connect to the docker API ... dockerDesktopLinuxEngine`, berarti Docker Desktop tidak jalan. Kalau GNS3 memakai GNS3 VM, container ada di dalam VM, bukan di Docker Desktop Windows.

Pencegahan: sebelum mematikan komputer, **Stop all nodes**, tutup project, baru shutdown.

### 10.5 Setelah GNS3 dinyalakan ulang / container dibuat ulang

Yang **hilang**: IP yang diset manual, rute manual, `sysctl -w`, `iptables`, proses (Kea, python server, iperf3), `tc`, paket yang dipasang `apk add` (python3, tmux, kea, iperf3).
Yang biasanya **aman**: topologi, `/etc/...`, `/root/...` (mis. `/root/myscripts`, config Kea).

Checklist urutan menyalakan:

1. Start semua node.
2. Router: IP (kalau tidak di `interfaces`), `ip_forward`, rute, NAT (RhodesIsland). Pasang `tc` hanya saat skenario.
3. Yan: `apk add kea-dhcp4` (jika `command not found`), `mkdir -p /var/lib/kea /run/kea /var/log/kea`, jalankan Kea.
4. Laterano: `udhcpc -i eth0` (harus 10.14.4.15), `apk add python3` jika hilang, `nohup /root/myscripts/start_http.sh ... &`.
5. Columbia: `ip -br addr` (10.14.3.x), default route, `ping 10.14.4.15`.
6. Uji `curl` ke `tes10000.bin`.

Buat `/root/setup.sh` per node supaya tinggal dijalankan (simpan di `/root`, persisten).

### 10.6 DHCP client tidak mendapat IP

- **Discover terlihat di `tcpdump` server tapi tidak ada Offer:** hampir pasti **Kea tidak berjalan**. `ss -ulnp | grep ':67'` kosong -> jalankan Kea. `tcpdump` melihat paket walau tidak ada proses yang membalas.
- `kea-dhcp4: command not found`: paket hilang, `apk add kea-dhcp4` (butuh internet; lepas `tc` kalau lambat).
- Kea jalan tapi diam: IP `eth0` server harus berada di subnet yang dideklarasikan (`10.14.4.2/24` untuk subnet 4.0/24). Cek log di foreground: `kea-dhcp4 -c /etc/kea/kea-dhcp4.conf` (pesan seperti `no suitable subnet`).
- Config JSON rusak: `kea-dhcp4 -t /etc/kea/kea-dhcp4.conf`. Output terpotong saat copy-paste (tanda `>`) bisa merusak JSON.
- `dhclient: command not found`: pakai `udhcpc -i eth0 -v`.
- Client beda segmen dengan server: DHCP broadcast tidak melewati router, perlu relay. Pada topologi ini setiap subnet punya DHCP server sendiri.
- Laterano tidak dapat 10.14.4.15: cek MAC di `reservations` sama dengan `ip link show eth0` di Laterano.

### 10.7 `curl` gagal

| Gejala | Penyebab | Solusi |
|---|---|---|
| `size=0B total=0.000000s` tanpa pesan | `-s` menyembunyikan error | Pakai `-sS` |
| `URL rejected: No host part` | `-o` terlewat, atau `[` tersisa di URL | Perintah standar bagian 2.7 |
| `Connection refused` (cepat, ~1 RTT) | Server HTTP tidak jalan | Di Laterano: `ss -tlnp \| grep 9999`; jalankan server via `nohup` |
| `Failed to connect ... after 0 ms` | Rute atau IP client tidak ada | `ip -br addr`, `ip route` di Columbia |
| `Connection timed out` | Jalur putus, rute router hilang, atau `tc` terlalu ketat | Ping dulu; periksa rute router; lepas `tc` untuk tes |
| `python3: command not found` | Paket hilang setelah container dibuat ulang | `apk add python3` (lepas `tc` supaya cepat) |

Ping berhasil belum membuktikan port 9999 terbuka: ping hanya menguji IP. `Connection refused` berarti paket sampai tapi tidak ada proses yang listen.

### 10.8 `tc` (netem/tbf)

- Cek aturan: `tc qdisc show dev eth1` dan `eth2`.
- Hapus semua: `tc qdisc del dev eth1 root` dan `tc qdisc del dev eth2 root`. Ini juga menghapus child (tbf/netem).
- `Error: Exclusivity flag on` -> sudah ada qdisc, `del` dulu.
- `Cannot delete qdisc with handle of zero` -> tidak ada aturan, aman.
- **Pasang `tc` di Skenario 3 membuat `apk`/`apt` dan ping sangat lambat.** Instal semua paket (python3, kea, iperf3, tmux) **sebelum** memasang gangguan.
- Wajib lepas `tc` sebelum mengukur baseline atau sebelum bagian berikutnya. Tiap bagian harus dimulai dari kondisi yang jelas.
- Kalau SYN tidak sampai pada Skenario 3: tbf dengan `latency` kecil bisa membuang paket saat antrean penuh; coba `latency` lebih besar. Modul memakai `latency 100ms`.

### 10.9 Wireshark

| Masalah | Solusi |
|---|---|
| Handshake tidak terlihat | Capture dimulai setelah curl, atau terscroll. Mulai capture dulu, `Ctrl+Home` |
| Hasil capture hilang | Jendela ditutup tanpa Save. Selalu `Ctrl+S` |
| Tidak ada paket | Link salah di-capture; hapus filter sementara |
| Filter kosong padahal seharusnya ada | Belum menekan Enter, field salah, atau capture salah (Columbia vs Laterano) |
| Dua koneksi dalam satu capture | Pakai `tcp.port == <port klien>` |
| Seq selalu 0 | Relative numbers aktif; baca `Sequence Number (raw)` |
| Kolom Info terpotong | Tambahkan kolom Ack/Seq/Len, lebarkan kolom |
| I/O Graph skala aneh / banyak garis | Hapus grafik sisa; naikkan interval |
| Angka I/O Graph janggal | Periksa Y Axis (AVG bukan SUM), Y Field, filter |
| Tombol start capture (sirip hiu) tidak jalan | Stop capture di GNS3, lalu Start lagi |
| Status bar tidak terlihat | View > Status Bar, maksimalkan jendela |

### 10.10 Salah ketik yang sering terjadi

- `mkdir -p a b` lalu `c` diketik terpisah (jadi baris perintah sendiri): satu perintah harus satu baris.
- `curl` tanpa `-o`.
- Menjalankan perintah lain di terminal yang sedang menjalankan server (`Ctrl+C` mematikan server). Gunakan terminal kedua atau `nohup`/`tmux`.
- Placeholder `[IP Prefix]` atau `<angka>` tidak diganti.
- Tabel Markdown rusak: satu baris tabel harus satu baris teks, ganti Enter dengan `<br>`, jangan ada baris kosong, awali dan akhiri dengan `|`.

---

## 11. Checklist Bukti (Sesuai Ketentuan Modul)

- Screenshot topologi GNS3 dan hasil uji konektivitas (ping, `ip route`, lease DHCP).
- Command konfigurasi penting dan tabel route, bukan hanya hasil ping.
- Capture Wireshark dengan filter dan status bar terlihat; satu screenshot boleh untuk beberapa parameter bila jelas.
- Pisahkan data: normal, loss 1%, CUBIC, Reno, TCP, UDP.
- Indikator probabilistik yang tidak muncul (mis. timeout retransmission) -> jelaskan apa yang diamati dan alasannya.
- Isi dengan **hasil aktual**, bukan angka perkiraan.
  
### 1.4 Referensi

- https://www.geeksforgeeks.org/computer-networks/open-systems-interconnection-model-osi/

- https://www.tutorialspoint.com/data_communication_computer_network/computer_network_models.htm

- https://www.geeksforgeeks.org/computer-networks/computer-network-models/

- https://www.lifewire.com/layers-of-the-osi-model-illustrated-818017

1. [Web Server](Web%20server/README.md)
2. [DNS (Domain Name System)](DNS/README.md)
3. [Reverse Proxy](Reverse%20Proxy/README.md)
