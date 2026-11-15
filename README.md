# Dokumentasi Mid-Project

**Mata kuliah:** Komunikasi Data dan Jaringan Komputer
**Alat:** Cisco Packet Tracer 9.0.1 — file `topologi_ets_baru.pkt`
**Skema subnetting yang dipilih:** **VLSM** pada blok induk `10.48.0.0/16`

## Daftar Isi

1. [Gambar topologi & alasan pemilihan skema](#1-gambar-topologi--alasan-pemilihan-skema-vlsm)
2. [Penjelasan desain topologi](#2-penjelasan-desain-topologi)
3. [Penjelasan semua konfigurasi](#3-penjelasan-semua-konfigurasi)
4. [Bukti pengujian mandiri](#4-bukti-pengujian-mandiri)

---

## 1. Gambar topologi & alasan pemilihan skema (VLSM)

![Topologi Mid-Project di Cisco Packet Tracer](screenshot/0-topologi-atas.jpeg)
![Topologi Mid-Project di Cisco Packet Tracer](screenshot/0-topologi-bawah.jpeg)

### 1.1 Alasan memilih VLSM

Kebutuhan host antar-LAN sangat timpang: dari **11.000 host** (Wi-Fi Publik Utara) sampai **20 host** (Server Balai Kota). Alasan memilih VLSM:

- **Efisien.** Tiap LAN mendapat blok sebesar kebutuhannya (host + 2, dibulatkan ke pangkat dua). Total 20 subnet (14 LAN + 6 link) hanya memakai **61.752** dari 65.536 alamat `10.48.0.0/16`, sisa **3.784** alamat. Jika dipaksa satu prefix sama (blok terbesar /18), 14 LAN butuh 14 × 16.384 alamat dan jauh melebihi satu blok /16.
- **Ada ruang tumbuh.** Sisa 3.784 alamat (`10.48.241.56` – `10.48.255.255`) bisa dipakai untuk link cadangan L7 (soal 7) dan perluasan lain.
- **Mudah diringkas (summarization).** Blok dialokasikan dari yang terbesar ke terkecil, sehingga setiap subnet selalu sejajar dengan batas bloknya dan tidak ada yang tumpang tindih. Hal ini sudah divalidasi lewat script: network ID, rentang host, dan broadcast ke-20 subnet konsisten.
- **Link antar-router memakai /30** (2 host) agar tidak membuang alamat.

### 1.2 Tabel kebutuhan subnet (Tabel 1B)

Kebutuhan alamat = jumlah host + 2 (network ID & broadcast), lalu dibulatkan ke pangkat dua terdekat.

| No | Bagian | Host | Kebutuhan (host+2) | Prefix | Ukuran blok | Subnet mask |
|---|---|---:|---:|---|---:|---|
| 1 | Wi-Fi Publik Utara | 11.000 | 11.002 | /18 | 16.384 | 255.255.192.0 |
| 2 | Wi-Fi Publik Selatan | 6.500 | 6.502 | /19 | 8.192 | 255.255.224.0 |
| 3 | CCTV Kota | 6.000 | 6.002 | /19 | 8.192 | 255.255.224.0 |
| 4 | Wi-Fi Publik Timur | 5.500 | 5.502 | /19 | 8.192 | 255.255.224.0 |
| 5 | Sensor dan SCADA Pelabuhan | 5.000 | 5.002 | /19 | 8.192 | 255.255.224.0 |
| 6 | Wi-Fi Publik Barat | 4.500 | 4.502 | /19 | 8.192 | 255.255.224.0 |
| 7 | Pegawai Balai Kota | 1.800 | 1.802 | /21 | 2.048 | 255.255.248.0 |
| 8 | Sensor Banjir Timur | 700 | 702 | /22 | 1.024 | 255.255.252.0 |
| 9 | Sensor Lalu Lintas Utara | 450 | 452 | /23 | 512 | 255.255.254.0 |
| 10 | Puskesmas dan Sekolah Barat | 220 | 222 | /24 | 256 | 255.255.255.0 |
| 11 | Server dan Storage DC | 180 | 182 | /24 | 256 | 255.255.255.0 |
| 12 | Kantor Pelabuhan | 120 | 122 | /25 | 128 | 255.255.255.128 |
| 13 | PJU Pintar Selatan | 90 | 92 | /25 | 128 | 255.255.255.128 |
| 14 | Server Balai Kota | 20 | 22 | /27 | 32 | 255.255.255.224 |

Total host = **42.080**. Blok induk: `10.48.0.0/16` (mask `255.255.0.0`, 10.48.0.0 – 10.48.255.255, 65.536 alamat).

### 1.3 Tabel subnetting VLSM (Soal 2)

Gateway setiap LAN = **alamat host pertama**. Pada link L1–L6, alamat host pertama dipakai router sisi atas (R-PUSAT, atau R-UTARA untuk L6) dan alamat kedua router sisi bawah.

| No | Subnet | Network ID | Prefix | Rentang host | Broadcast | Gateway |
|---|---|---|---|---|---|---|
| 1 | Wi-Fi Publik Utara | 10.48.0.0 | /18 | 10.48.0.1 - 10.48.63.254 | 10.48.63.255 | 10.48.0.1 |
| 2 | Wi-Fi Publik Selatan | 10.48.64.0 | /19 | 10.48.64.1 - 10.48.95.254 | 10.48.95.255 | 10.48.64.1 |
| 3 | CCTV Kota | 10.48.96.0 | /19 | 10.48.96.1 - 10.48.127.254 | 10.48.127.255 | 10.48.96.1 |
| 4 | Wi-Fi Publik Timur | 10.48.128.0 | /19 | 10.48.128.1 - 10.48.159.254 | 10.48.159.255 | 10.48.128.1 |
| 5 | Sensor dan SCADA Pelabuhan | 10.48.160.0 | /19 | 10.48.160.1 - 10.48.191.254 | 10.48.191.255 | 10.48.160.1 |
| 6 | Wi-Fi Publik Barat | 10.48.192.0 | /19 | 10.48.192.1 - 10.48.223.254 | 10.48.223.255 | 10.48.192.1 |
| 7 | Pegawai Balai Kota | 10.48.224.0 | /21 | 10.48.224.1 - 10.48.231.254 | 10.48.231.255 | 10.48.224.1 |
| 8 | Sensor Banjir Timur | 10.48.232.0 | /22 | 10.48.232.1 - 10.48.235.254 | 10.48.235.255 | 10.48.232.1 |
| 9 | Sensor Lalu Lintas Utara | 10.48.236.0 | /23 | 10.48.236.1 - 10.48.237.254 | 10.48.237.255 | 10.48.236.1 |
| 10 | Puskesmas dan Sekolah Barat | 10.48.238.0 | /24 | 10.48.238.1 - 10.48.238.254 | 10.48.238.255 | 10.48.238.1 |
| 11 | Server dan Storage DC | 10.48.239.0 | /24 | 10.48.239.1 - 10.48.239.254 | 10.48.239.255 | 10.48.239.1 |
| 12 | Kantor Pelabuhan | 10.48.240.0 | /25 | 10.48.240.1 - 10.48.240.126 | 10.48.240.127 | 10.48.240.1 |
| 13 | PJU Pintar Selatan | 10.48.240.128 | /25 | 10.48.240.129 - 10.48.240.254 | 10.48.240.255 | 10.48.240.129 |
| 14 | Server Balai Kota | 10.48.241.0 | /27 | 10.48.241.1 - 10.48.241.30 | 10.48.241.31 | 10.48.241.1 |
| 15 | L1 (R-PUSAT ↔ R-DC) | 10.48.241.32 | /30 | 10.48.241.33 - 10.48.241.34 | 10.48.241.35 | — |
| 16 | L2 (R-PUSAT ↔ R-UTARA) | 10.48.241.36 | /30 | 10.48.241.37 - 10.48.241.38 | 10.48.241.39 | — |
| 17 | L3 (R-PUSAT ↔ R-SELATAN) | 10.48.241.40 | /30 | 10.48.241.41 - 10.48.241.42 | 10.48.241.43 | — |
| 18 | L4 (R-PUSAT ↔ R-TIMUR) | 10.48.241.44 | /30 | 10.48.241.45 - 10.48.241.46 | 10.48.241.47 | — |
| 19 | L5 (R-PUSAT ↔ R-BARAT) | 10.48.241.48 | /30 | 10.48.241.49 - 10.48.241.50 | 10.48.241.51 | — |
| 20 | L6 (R-UTARA ↔ R-PELABUHAN) | 10.48.241.52 | /30 | 10.48.241.53 - 10.48.241.54 | 10.48.241.55 | — |

**Penetapan IP antarmuka link:**

| Link | Subnet | Sisi atas | Sisi bawah |
|---|---|---|---|
| L1 | 10.48.241.32/30 | R-PUSAT `.33` | R-DC `.34` |
| L2 | 10.48.241.36/30 | R-PUSAT `.37` | R-UTARA `.38` |
| L3 | 10.48.241.40/30 | R-PUSAT `.41` | R-SELATAN `.42` |
| L4 | 10.48.241.44/30 | R-PUSAT `.45` | R-TIMUR `.46` |
| L5 | 10.48.241.48/30 | R-PUSAT `.49` | R-BARAT `.50` |
| L6 | 10.48.241.52/30 | R-UTARA `.53` | R-PELABUHAN `.54` |

**Alamat tambahan (tidak dihitung saat UTS, K = 6):**

| Keperluan | Alamat |
|---|---|
| Link R-PUSAT ↔ R-ISP (`203.0.113.(4×6)/30`) | `203.0.113.24/30` — R-ISP `.25`, R-PUSAT `.26` |
| Server internet | `8.8.8.0/24` — server `8.8.8.8`, gateway `8.8.8.1` (R-ISP) |
| Tunnel GRE (`192.168.K.0/30`) | `192.168.6.0/30` — R-PUSAT `.1`, R-PELABUHAN `.2` |

### 1.4 Tabel revisi terhadap jawaban UTS (Soal 2)

Jawaban UTS masih menggabungkan LAN per router dan memakai oktet pertama `xx`. Perbaikan yang dilakukan:

| Subnet | Jawaban UTS | Perbaikan | Alasan |
|---|---|---|---|
| Wi-Fi Publik Utara | xx.48.0.0/17 (digabung R-UTARA) | 10.48.0.0/18 | Dipisah jadi LAN sendiri; 11.000 host cukup di /18 (16.382 host), /17 berlebihan |
| Sensor Lalu Lintas Utara | xx.48.0.0/17 (digabung) | 10.48.236.0/23 | Belum punya blok sendiri; 450 host → /23 (510 host) |
| Sensor dan SCADA Pelabuhan | xx.48.64.0/19 (digabung R-PELABUHAN) | 10.48.160.0/19 | Blok UTS berada di dalam R-UTARA /17 sehingga **tumpang tindih**; dipindah dan dipisah dari kantor |
| Kantor Pelabuhan | xx.48.64.0/19 (digabung) | 10.48.240.0/25 | Belum punya blok sendiri; 120 host → /25 (126 host) |
| CCTV Kota | xx.48.128.0/19 (digabung R-DC) | 10.48.96.0/19 | Dipisah dari LAN server/storage; urutan mengikuti kebutuhan host terbesar |
| Server dan Storage DC | xx.48.128.0/19 (digabung) | 10.48.239.0/24 | Belum punya blok sendiri; 180 host → /24 (254 host) |
| Wi-Fi Publik Barat | xx.48.160.0/19 (digabung R-BARAT) | 10.48.192.0/19 | Dipisah dari LAN puskesmas/sekolah; prefix tetap /19 |
| Puskesmas dan Sekolah Barat | xx.48.160.0/19 (digabung) | 10.48.238.0/24 | Belum punya blok sendiri; 220 host → /24 |
| Wi-Fi Publik Timur | xx.48.192.0/19 (digabung R-TIMUR) | 10.48.128.0/19 | Dipisah dari LAN sensor banjir; prefix tetap /19 |
| Sensor Banjir Timur | xx.48.192.0/19 (digabung) | 10.48.232.0/22 | Belum punya blok sendiri; 700 host → /22 (1.022 host) |
| Wi-Fi Publik Selatan | xx.48.224.0/19 (digabung R-SELATAN) | 10.48.64.0/19 | Dipisah dari LAN PJU; urutan terbesar kedua |
| PJU Pintar Selatan | xx.48.224.0/19 (digabung) | 10.48.240.128/25 | Belum punya blok sendiri; 90 host → /25 |
| Pegawai Balai Kota (PBK) | xx.49.0.0/21 | 10.48.224.0/21 | Prefix tetap; network ID dipindah karena `xx.49.x` di luar blok induk `/16` |
| Server Balai Kota (SBK) | xx.49.8.0/27 | 10.48.241.0/27 | Prefix tetap; dipindah masuk blok induk `10.48.0.0/16` |
| L1 – L6 | Belum dicantumkan | 10.48.241.32/30 … 10.48.241.52/30 | Link antar-router butuh subnet sendiri; /30 = 2 host |

**Perubahan umum:**

| Aspek | UTS | Perbaikan | Alasan |
|---|---|---|---|
| Oktet pertama | `xx.` | `10.` | Memakai rentang privat 10.0.0.0/8; oktet kedua tetap 48 (8 × kelompok 6) |
| Dasar pembagian | Per router (8 baris) | Per LAN (14 LAN + 6 link = 20 baris) | Kebutuhan dihitung per LAN sesuai topologi |
| Urutan alokasi | Per router | Host terbesar → terkecil, lalu L1–L6 | Mencegah tumpang tindih dan menjaga batas blok |
| Tabel kebutuhan | Tidak ada | Tabel 1B (14 LAN) | Merinci dasar perhitungan |
| Gateway | Tidak ada | Host pertama tiap LAN | Diwajibkan di soal 2 |
| Ringkasan "Sisa 23.456" | 65.536 − 42.080 | **3.784** = 65.536 − 61.752 | 23.456 belum memperhitungkan pembulatan blok, network ID, broadcast, dan link |
| "Host digunakan 65.534" | Kapasitas /16 | Kapasitas usable 20 subnet = **61.712** | 65.534 bukan jumlah host terpakai setelah subnetting |


---

## 2. Penjelasan desain topologi

### 2.1 Backbone (mengikuti Gambar 1 soal)

Topologi berbentuk **star dua tingkat** dengan **R-PUSAT** (Balai Kota) sebagai pusat:

- **R-PUSAT** terhubung ke R-DC (L1), R-UTARA (L2), R-SELATAN (L3), R-TIMUR (L4), R-BARAT (L5).
- **R-PELABUHAN** terhubung ke **R-UTARA** (L6), sehingga trafik pelabuhan ke Balai Kota normalnya melewati R-UTARA.
- **R-ISP** terhubung ke R-PUSAT (`203.0.113.24/30`) dan ke **SRV-INTERNET** `8.8.8.8`.
- Semua link backbone memakai kabel tembaga (copper) pada antarmuka Gigabit.

### 2.2 Desain tiap lokasi

Tiap LAN dipisahkan sendiri-sendiri melalui switch 2960-24TT sendiri yang terhubung ke satu port router, sehingga domain broadcast tidak tercampur. Setiap LAN cukup dipasang **1 perangkat perwakilan**; perhitungan subnet tetap memakai jumlah host penuh.

| Lokasi / Router | LAN | Port router | Switch | Perangkat perwakilan | Media |
|---|---|---|---|---|---|
| **Balai Kota** (R-PUSAT, 2911) | Pegawai Balai Kota | Gi0/1 | SW-PEGAWAI-BALAIKOTA | PC (`Pegawai Balai Kota`) | Kabel |
| | Server Balai Kota | Gi0/2 | SW-SERVER-BALAIKOTA | Server (`10.48.241.2`) | Kabel |
| **R-DC** | CCTV Kota | Gi3/0 | SW-CCTV-KOTA | PC (`CCTV Kota`) | Kabel |
| | Server dan Storage DC | Gi4/0 | SW-SERVER-STORAGE-DC | Server (`10.48.239.10`) | Kabel |
| **R-BARAT** | Wi-Fi Publik Barat | Gi3/0 | SW-WIFIPUBLIK-BARAT → AP-WIFI-BARAT | Smartphone | **Wireless** |
| | Puskesmas dan Sekolah Barat | Gi4/0 | SW-PUSKESMAS-SEKOLAH-BARAT | PC | Kabel |
| **R-UTARA** | Wi-Fi Publik Utara | Gi3/0 | SW-WIFI-PUBLIK-UTARA → AP-WIFI-UTARA | Tablet PC | **Wireless** |
| | Sensor Lalu Lintas Utara | Gi5/0 | SW-SENSOR-LALULINTAS-UTARA | PC | Kabel |
| **R-TIMUR** | Wi-Fi Publik Timur | Gi3/0 | SW-WIFI-PUBLIK-TIMUR → AP-WIFI-TIMUR | Tablet PC | **Wireless** |
| | Sensor Banjir Timur | Gi4/0 | SW-SENSOR-BANJIR-TIMUR | PC | Kabel |
| **R-SELATAN** | Wi-Fi Publik Selatan | Gi3/0 | SW-WIFI-PUBLIK-SELATAN → AP-WIFI-SELATAN | Smartphone | **Wireless** |
| | PJU Pintar Selatan | Gi4/0 | SW-PJU-SELATAN | PC | Kabel |
| **R-PELABUHAN** (2911) | Sensor dan SCADA Pelabuhan | Gi0/1 | SW-SCADA-PELABUHAN | PC | Kabel |
| | Kantor Pelabuhan | Gi0/2 | SW-KANTOR-PELABUHAN | PC | Kabel |

  **Alasan:**

- **Jenis perangkat.** Server dipakai untuk LAN yang menyimpan layanan (Server Balai Kota, Server dan Storage DC); PC untuk sensor, CCTV, dan kantor; **smartphone/tablet + Access Point** untuk Wi-Fi Publik karena pengguna Wi-Fi memang perangkat mobile.
- **Media wireless** dipakai hanya pada empat LAN Wi-Fi Publik (Barat, Utara, Timur, Selatan). Sisanya kabel karena sensor/CCTV/server butuh koneksi stabil dan tidak boleh bergantung pada sinyal.
- **Dua LAN per router → dua switch.** Satu switch per LAN menjaga LAN tetap terpisah secara fisik tanpa perlu VLAN, dan menyederhanakan pengujian.
- **Penambahan port router.** Router-PT bawaan hanya punya sedikit port, sehingga ditambah modul port Gigabit (slot 2/0–9/0) untuk link backbone dan LAN. **R-PUSAT (2911)** butuh 5 link backbone sehingga ditambah dua kartu switch **HWIC-4ESW**; port `Fa0/0/0–Fa0/0/3` dan `Fa0/1/0` dipakai sebagai _access port_ pada VLAN 11–15 (satu VLAN per link), lalu tiap VLAN diberi IP lewat _interface VLAN_ (SVI). Gi0/0 untuk R-ISP, Gi0/1 dan Gi0/2 untuk dua LAN Balai Kota.
- **R-PELABUHAN memakai 2911** karena hanya memerlukan 3 port Gigabit bawaan (L6 + 2 LAN) tanpa tambahan modul, dan dibutuhkan untuk menjalankan tunnel GRE.
- **Penamaan perangkat** mengikuti lokasi (`R-PUSAT`, `SW-CCTV-KOTA`, `AP-WIFI-UTARA`, dsb.) dan setiap LAN diberi label berisi Network ID/prefix dan jumlah host (terlihat pada Gambar 1 di atas).

---

## 3. Penjelasan semua konfigurasi

### 3.1 IP statis pada antarmuka router dan server

| Perangkat | Antarmuka | Alamat | Keterangan |
|---|---|---|---|
| R-ISP | Gi0/0 | 203.0.113.25/30 | Ke R-PUSAT |
| R-ISP | Gi1/0 | 8.8.8.1/24 | Gateway server internet |
| SRV-INTERNET | Fa0 | 8.8.8.8/24, gw 8.8.8.1 | Server internet |
| R-PUSAT | Gi0/0 | 203.0.113.26/30 | Ke R-ISP (**NAT outside**) |
| R-PUSAT | Gi0/1 | 10.48.224.1/21 | LAN Pegawai Balai Kota |
| R-PUSAT | Gi0/2 | 10.48.241.1/27 | LAN Server Balai Kota |
| R-PUSAT | Vlan11 / 12 / 13 / 14 / 15 | .33 / .49 / .37 / .45 / .41 (/30) | L1 (DC) / L5 (Barat) / L2 (Utara) / L4 (Timur) / L3 (Selatan) |
| R-DC | Gi2/0 · Gi3/0 · Gi4/0 | 10.48.241.34/30 · 10.48.96.1/19 · 10.48.239.1/24 | L1 · CCTV · Server DC |
| R-UTARA | Gi2/0 · Gi3/0 · Gi4/0 · Gi5/0 | 10.48.241.38/30 · 10.48.0.1/18 · 10.48.241.53/30 · 10.48.236.1/23 | L2 · Wi-Fi · L6 · Sensor lalin |
| R-BARAT | Gi2/0 · Gi3/0 · Gi4/0 | 10.48.241.50/30 · 10.48.192.1/19 · 10.48.238.1/24 | L5 · Wi-Fi · Puskesmas/Sekolah |
| R-TIMUR | Gi2/0 · Gi3/0 · Gi4/0 | 10.48.241.46/30 · 10.48.128.1/19 · 10.48.232.1/22 | L4 · Wi-Fi · Sensor banjir |
| R-SELATAN | Gi2/0 · Gi3/0 · Gi4/0 | 10.48.241.42/30 · 10.48.64.1/19 · 10.48.240.129/25 | L3 · Wi-Fi · PJU |
| R-PELABUHAN | Gi0/0 · Gi0/1 · Gi0/2 | 10.48.241.54/30 · 10.48.160.1/19 · 10.48.240.1/25 | L6 · SCADA · Kantor |
| Server Balai Kota | Fa0 | 10.48.241.2/27, gw 10.48.241.1 | Statis |
| Server dan Storage DC | Fa0 | 10.48.239.10/24, gw 10.48.239.1 | Statis (DHCP server CCTV) |

Alamat router adalah **host pertama** tiap LAN (gateway); alamat server dipilih di luar rentang DHCP.

### 3.2 Static routing (Soal 3)

Prinsip: **default route hanya ke router di atasnya**; router pusat (R-PUSAT, R-UTARA) punya static route ke jaringan di bawahnya.

| Router | Konfigurasi |
|---|---|
| R-DC | `ip route 0.0.0.0 0.0.0.0 10.48.241.33` |
| R-BARAT | `ip route 0.0.0.0 0.0.0.0 10.48.241.49` |
| R-TIMUR | `ip route 0.0.0.0 0.0.0.0 10.48.241.45` |
| R-SELATAN | `ip route 0.0.0.0 0.0.0.0 10.48.241.41` |
| R-PELABUHAN | `ip route 0.0.0.0 0.0.0.0 10.48.241.53` (ke R-UTARA) |
| R-UTARA | `ip route 0.0.0.0 0.0.0.0 10.48.241.37` (ke R-PUSAT) <br>`ip route 10.48.160.0 255.255.224.0 10.48.241.54` <br>`ip route 10.48.240.0 255.255.255.128 10.48.241.54` |
| R-PUSAT | `ip route 0.0.0.0 0.0.0.0 203.0.113.25` (ke R-ISP) |

R-PUSAT: static route ke semua LAN di bawahnya (12 route):

```
ip route 10.48.96.0   255.255.224.0   10.48.241.34   ! CCTV Kota        -> R-DC
ip route 10.48.239.0  255.255.255.0   10.48.241.34   ! Server & Storage -> R-DC
ip route 10.48.0.0    255.255.192.0   10.48.241.38   ! Wi-Fi Utara      -> R-UTARA
ip route 10.48.236.0  255.255.254.0   10.48.241.38   ! Sensor Lalin     -> R-UTARA
ip route 10.48.160.0  255.255.224.0   10.48.241.38   ! SCADA Pelabuhan  -> R-UTARA (-> R-PELABUHAN)
ip route 10.48.240.0  255.255.255.128 10.48.241.38   ! Kantor Pelabuhan -> R-UTARA (-> R-PELABUHAN)
ip route 10.48.64.0   255.255.224.0   10.48.241.42   ! Wi-Fi Selatan    -> R-SELATAN
ip route 10.48.240.128 255.255.255.128 10.48.241.42  ! PJU              -> R-SELATAN
ip route 10.48.128.0  255.255.224.0   10.48.241.46   ! Wi-Fi Timur      -> R-TIMUR
ip route 10.48.232.0  255.255.252.0   10.48.241.46   ! Sensor Banjir    -> R-TIMUR
ip route 10.48.192.0  255.255.224.0   10.48.241.50   ! Wi-Fi Barat      -> R-BARAT
ip route 10.48.238.0  255.255.255.0   10.48.241.50   ! Puskesmas/Sekolah-> R-BARAT
```

Dengan skema ini seluruh LAN dapat saling berkomunikasi: trafik naik lewat default route sampai R-PUSAT, lalu turun lewat static route. LAN Balai Kota (Gi0/1, Gi0/2) dan link L1–L5 terhubung langsung di R-PUSAT sehingga tidak perlu route.

### 3.3 DHCP (Soal 4)

Antarmuka router dan seluruh server memakai IP statis, sedangkan perangkat pengguna memperoleh IP secara dinamis melalui DHCP lengkap dengan gateway dan DNS. Pada setiap pool hanya alamat **gateway** (host pertama subnet) yang dikecualikan (`excluded-address`). Alamat statis lain tidak berada dalam rentang yang dibagikan karena ditempatkan pada subnet terpisah: Server Balai Kota (`10.48.241.0/27`), Server dan Storage DC (`10.48.239.0/24`), dan link antar-router (`/30`).

| Router | Pool | Network | Gateway | Excluded |
|---|---|---|---|---|
| R-PUSAT | PEGAWAI-BALAIKOTA | 10.48.224.0/21 | 10.48.224.1 | 10.48.224.1 |
| R-UTARA | WIFI-UTARA | 10.48.0.0/18 | 10.48.0.1 | 10.48.0.1 |
| R-UTARA | SENSOR-LALULINTAS-UTARA | 10.48.236.0/23 | 10.48.236.1 | 10.48.236.1 |
| R-BARAT | WIFI-BARAT | 10.48.192.0/19 | 10.48.192.1 | 10.48.192.1 |
| R-BARAT | PUSKESMAS-SEKOLAH-BARAT | 10.48.238.0/24 | 10.48.238.1 | 10.48.238.1 |
| R-TIMUR | WIFI-TIMUR | 10.48.128.0/19 | 10.48.128.1 | 10.48.128.1 |
| R-TIMUR | SENSOR-BANJIR-TIMUR | 10.48.232.0/22 | 10.48.232.1 | 10.48.232.1 |
| R-SELATAN | WIFI-SELATAN | 10.48.64.0/19 | 10.48.64.1 | 10.48.64.1 |
| R-SELATAN | PJU-SELATAN | 10.48.240.128/25 | 10.48.240.129 | 10.48.240.129 |
| R-PELABUHAN | SCADA-PELABUHAN | 10.48.160.0/19 | 10.48.160.1 | 10.48.160.1 |
| R-PELABUHAN | KANTOR-PELABUHAN | 10.48.240.0/25 | 10.48.240.1 | 10.48.240.1 |
| Server DC | CCTV-KOTA | 10.48.96.0/19 | 10.48.96.1 | (tidak ada; pool mulai 10.48.96.2) |

Semua pool memberikan **DNS `8.8.8.8`** untuk sementara. Setelah DNS internal (soal 8) aktif, DNS pada pool diganti menjadi `10.48.239.10`.

Contoh konfigurasi (R-UTARA):

```
ip dhcp excluded-address 10.48.0.1
ip dhcp pool WIFI-UTARA
 network 10.48.0.0 255.255.192.0
 default-router 10.48.0.1
 dns-server 8.8.8.8
```

**Kasus khusus: subnet CCTV Kota (R-DC).** Alamat CCTV diberikan oleh **server DHCP di LAN Server dan Storage DC**, bukan oleh router. R-DC tidak memiliki pool DHCP. Pada antarmuka CCTV dipasang **DHCP relay**:

```
interface GigabitEthernet3/0
 ip address 10.48.96.1 255.255.224.0
 ip helper-address 10.48.239.10
```

Klien CCTV mengirim DHCP Discover secara broadcast, dan broadcast tidak diteruskan oleh router. Dengan `ip helper-address`, R-DC mengubahnya menjadi **unicast** menuju `10.48.239.10` dan mengisi field `giaddr` dengan `10.48.96.1`. Server menggunakan `giaddr` untuk memilih pool yang sesuai (CCTV-KOTA), lalu balasannya dikirim kembali melalui R-DC ke klien. Server berada pada subnet yang terhubung langsung dengan R-DC, sehingga tidak diperlukan static route tambahan.

Pada server `10.48.239.10` (Services → DHCP) dibuat pool **CCTV-KOTA**: network `10.48.96.0/19`, default gateway `10.48.96.1`, DNS `8.8.8.8`, alamat awal `10.48.96.2`, maksimum 6.000 pengguna.

### 3.4 NAT (Soal 5)

Seluruh host mengakses server `8.8.8.8` memakai **satu IP publik** milik R-PUSAT (`203.0.113.26`) dengan **PAT/overload**. R-ISP tidak punya rute ke alamat privat (tidak ada static route 10.x di R-ISP).

```
access-list 1 permit 10.48.0.0 0.15.255.255

interface GigabitEthernet0/0
 ip nat outside
interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat inside
interface Vlan11 ... Vlan15
 ip nat inside

ip nat inside source list 1 interface GigabitEthernet0/0 overload
ip route 0.0.0.0 0.0.0.0 203.0.113.25
```

- ACL 1 mengizinkan 10.48.0.0 – 10.63.255.255, yang mencakup seluruh blok `10.48.0.0/16`.
- Antarmuka `inside` adalah semua sisi dalam (LAN Balai Kota dan Vlan L1–L5); `outside` hanya Gi0/0 menuju ISP.
- `overload` memetakan banyak host ke satu IP publik dengan membedakan nomor port.

### 3.5 Konfigurasi dasar lain

- Hostname semua perangkat sudah diganti sesuai lokasi (R-PUSAT, R-DC, dst.) dan tiap antarmuka diberi `description` (mis. `L1-KE-R-PUSAT`, `KE-SW-CCTV-KOTA`).
- Switch 2960 memakai konfigurasi default (tanpa VLAN) karena satu switch = satu LAN.
- Access Point memakai konfigurasi default dan perangkat mobile dihubungkan secara wireless.

### 3.6 Tunnel GRE (Soal 6): belum berhasil

**Status.** Tunnel GRE antara R-PUSAT dan R-PELABUHAN berhasil dibentuk (Tunnel0 `up, line protocol is up` di kedua router), tetapi lalu lintas **belum berhasil melewati tunnel**. Syarat soal bahwa trafik subnet Balai Kota menuju subnet Pelabuhan harus melewati tunnel **belum terpenuhi**. Penyebab pasti kegagalan belum berhasil dibuktikan.

**Rancangan.** Alamat tambahan di luar Tabel 3 diambil dari blok yang masih kosong pada `10.48.241.0/24`:

| Keterangan | Network | Prefix | Alamat |
|---|---|---|---|
| Tunnel GRE | 10.48.241.56 | /30 | R-PUSAT `.57`, R-PELABUHAN `.58` |
| Loopback endpoint tunnel | 10.48.241.60 | /30 | R-PUSAT `.61`, R-PELABUHAN `.62` |

Rancangan routing: rute statis dari R-PUSAT ke subnet Pelabuhan (`10.48.160.0/19`, `10.48.240.0/25`) melalui `10.48.241.58`, dan rute balik dari R-PELABUHAN ke subnet Balai Kota (`10.48.224.0/21`, `10.48.241.0/27`) melalui `10.48.241.57`.

**Kronologi pengujian.**

| No | Percobaan | Hasil |
|---|---|---|
| 1 | `tunnel source 10.48.241.37` (IP link L2 di R-PUSAT) | Ditolak (`Invalid input`). Tunnel source terbaca `UNKNOWN`, line protocol down. |
| 2 | `tunnel source Vlan13` (antarmuka yang memegang IP tersebut) | Ditolak. Hasil sama. |
| 3 | `tunnel source ?` | Hanya Ethernet, FastEthernet, GigabitEthernet, Loopback, dan Serial yang diterima. R-PUSAT memakai antarmuka VLAN (SVI) untuk link antar-router, sehingga tidak ada antarmuka yang dapat dipakai langsung. |
| 4 | Loopback0 sebagai source dan destination, ditambah rute `/32` ke loopback lawan di R-PUSAT, R-UTARA, dan R-PELABUHAN | Tunnel0 `up/up` di kedua router. |
| 5 | Ping extended antar-loopback (dari `10.48.241.62` ke `.61`, dan sebaliknya) | Berhasil 5/5. Jalur fisik antar-endpoint berfungsi. |
| 6 | Ping `10.48.241.58` dari R-PUSAT | Gagal (0/5), berulang. Counter `packets input/output` tetap 0. |
| 7 | Pemeriksaan ACL | Hanya ACL 1 (untuk NAT) dan tidak dipasang sebagai `access-group`. Tidak ada yang memblokir GRE. |
| 8 | Tunnel0 dibuat ulang di R-PUSAT dan R-PELABUHAN | `[ISI hasil ping .57 <-> .58 setelah kedua sisi dibuat ulang]` |
| 9 | Simulation Mode | Tidak ada paket tampil. Tidak konklusif karena diduga terkait mode dan filter event. |

**Analisis.** Karena ping antar-loopback berhasil dua arah, jalur underlay (rute dan kabel) dapat dikesampingkan. Karena Tunnel0 `up/up` dan ACL tidak ada, konfigurasi dasar tunnel juga tampak benar. Yang gagal hanya pengiriman paket melalui antarmuka tunnel itu sendiri. Beberapa dugaan, **tidak ada yang terbukti**:

1. Keterbatasan atau bug Packet Tracer dalam menangani GRE pada router yang memakai antarmuka VLAN (SVI).
2. Sisa kondisi tunnel yang rusak akibat konfigurasi awal bersumber `UNKNOWN`.
3. Perbedaan perilaku simulator pada ping yang berasal dari router sendiri.

**Dampak.** Selama tunnel tidak berfungsi, trafik Balai Kota ke Pelabuhan berjalan lewat jalur fisik melalui R-UTARA (rute statis via `10.48.241.38`).

**Penjelasan teori (diminta soal).**

*Mengapa router memilih tunnel dibanding jalur lain ke Pelabuhan.* Router memilih rute dengan *longest prefix match*. Bila prefix sama, dipilih *administrative distance* terkecil. Rute statis (AD 1) lebih diutamakan daripada OSPF (110) atau RIP (120). Dengan rute statis ke subnet Pelabuhan yang menuju ujung tunnel (`10.48.241.58`), paket dibungkus GRE dan dikirim ke endpoint lawan, sedangkan R-UTARA hanya meneruskan paket GRE tanpa melihat isinya. Penjelasan ini berlaku bila tunnel berfungsi.

*Mengapa GRE tidak mengenkripsi.* GRE hanya melakukan *encapsulation*: paket asli dibungkus header GRE (protokol 47) dan header IP luar. Isi tidak diacak, dan tidak ada kunci, autentikasi, atau pengecekan integritas. Siapa pun yang menangkap paket di jalur, misalnya di R-UTARA, dapat membaca isinya. Enkripsi memerlukan IPsec (ESP).

**Bonus IPsec.** Tidak dikerjakan karena tunnel GRE sebagai dasarnya belum berfungsi. Rancangan yang akan dipakai adalah ISAKMP (AES-256, SHA, pre-shared key, group 2) dan transform-set `esp-aes 256 esp-sha-hmac`. ACL hanya mencocokkan protokol GRE antar-loopback (`10.48.241.61` ↔ `10.48.241.62`), dan crypto map dipasang di antarmuka menuju R-UTARA. Rancangan ini belum diuji.

### 3.7 Dynamic routing OSPF (Soal 7)

![topologi ospf](screenshot/7-topologi-ospf.png)
![topologi ospf](screenshot/7-topologi-ospf1.png)


Mengganti seluruh static route dengan dynamic routing OSPF, dengan ketentuan:
- Menambahkan link cadangan **L7** antara R-DC dan R-UTARA.
- Router tidak mengirim pesan OSPF ke LAN pengguna.
- Default route ke internet hanya dikonfigurasi di R-PUSAT, router lain mendapatkannya lewat OSPF.
- Jika salah satu link backbone putus, konektivitas pulih otomatis lewat jalur lain.

### Pengalamatan Link L7

| Link | Network | R-DC | R-UTARA |
|---|---|---|---|
| L7 (cadangan) | `10.48.241.56/30` | `10.48.241.57` (Gi5/0) | `10.48.241.58` (Gi6/0) |

### Konfigurasi OSPF

Ganti static route menjadi OSPF

| Router | Router ID | Interface non-passive (link) | Network yang diiklankan |
|---|---|---|---|
| R-PUSAT | 1.1.1.1 | Vlan11 - Vlan15 | L1-L5, `10.48.224.0/21`, `10.48.241.0/27` |
| R-UTARA | 2.2.2.2 | Gi2/0, Gi4/0, Gi6/0 | L2, L6, L7, `10.48.0.0/18`, `10.48.236.0/23` |
| R-PELABUHAN | 3.3.3.3 | Gi0/0 | L6, `10.48.160.0/19`, `10.48.240.0/25` |
| R-DC | 4.4.4.4 | Gi2/0, Gi5/0 | L1, L7, `10.48.239.0/24`, `10.48.96.0/19` |
| R-BARAT | 5.5.5.5 | Gi2/0 | L5, `10.48.192.0/19`, `10.48.238.0/24` |
| R-TIMUR | 6.6.6.6 | Gi2/0 | L4, `10.48.128.0/19`, `10.48.232.0/22` |
| R-SELATAN | 7.7.7.7 | Gi2/0 | L3, `10.48.64.0/19`, `10.48.240.128/25` |

Contoh konfigurasi (R-PUSAT):
```
ip route 0.0.0.0 0.0.0.0 203.0.113.25
!
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface Vlan11
 no passive-interface Vlan12
 no passive-interface Vlan13
 no passive-interface Vlan14
 no passive-interface Vlan15
 network 10.48.241.32 0.0.0.3 area 0
 network 10.48.241.36 0.0.0.3 area 0
 network 10.48.241.40 0.0.0.3 area 0
 network 10.48.241.44 0.0.0.3 area 0
 network 10.48.241.48 0.0.0.3 area 0
 network 10.48.224.0 0.0.7.255 area 0
 network 10.48.241.0 0.0.0.31 area 0
 default-information originate
```

Contoh konfigurasi router lain (R-BARAT):
```
router ospf 1
 router-id 5.5.5.5
 passive-interface default
 no passive-interface GigabitEthernet2/0
 network 10.48.241.48 0.0.0.3 area 0
 network 10.48.192.0 0.0.31.255 area 0
 network 10.48.238.0 0.0.0.255 area 0
```

**Penjelasan:**
- `router-id` unik di tiap router sebagai identitas OSPF.
- `passive-interface default` mematikan hello di semua interface, `no passive-interface` hanya menghidupkannya di link antar-router.
- `network ... area 0` mengiklankan subnet, termasuk subnet LAN yang passive.
- `default-information originate` hanya di R-PUSAT.

### 3.8 Layanan aplikasi Smart City (Soal 8)

Menyediakan layanan aplikasi smart city dengan domain internal `k6.smartcity.lab`:
- **DNS** di LAN Server dan Storage DC, dengan nama `www`, `ftp`, dan `mail` yang dapat diakses dari seluruh wilayah.
- **Web** di LAN Server Balai Kota berupa portal Smart City Surabaya berisi nama kelompok, dapat dibuka dari seluruh wilayah dengan nama domain.
- **FTP** di LAN Server dan Storage DC untuk menyimpan rekaman CCTV dengan akun login, dan perangkat di LAN CCTV Kota dapat mengunggah ke sana.
- **Email** antar pegawai Balai Kota dan Kantor Pelabuhan.

### Pengalamatan Server

| Server | LAN | IP | Mask | Gateway | DNS | Layanan |
|---|---|---|---|---|---|---|
| Server dan Storage DC | `10.48.239.0/24` | `10.48.239.10` | `255.255.255.0` | `10.48.239.1` | `10.48.239.10` | DNS, FTP, Email |
| Server Balai Kota | `10.48.241.0/27` | `10.48.241.2` | `255.255.255.224` | `10.48.241.1` | `10.48.239.10` | HTTP |

### Konfigurasi

**1. DNS (Server DC, Services → DNS)**

| Name | Type | Address |
|---|---|---|
| `www.k6.smartcity.lab` | A | `10.48.241.2` |
| `ftp.k6.smartcity.lab` | A | `10.48.239.10` |
| `mail.k6.smartcity.lab` | A | `10.48.239.10` |

DNS Server `10.48.239.10` dibagikan ke klien lewat pool DHCP, contoh di router:
```
ip dhcp pool PEGAWAI-BALAIKOTA
 dns-server 10.48.239.10
```
Pool yang sama diubah untuk LAN Puskesmas Barat, Kantor Pelabuhan, dan LAN CCTV (pool di Server DC).

**2. Web (Server Balai Kota, Services → HTTP)**

HTTP diaktifkan dan `index.html` diganti:
```html
<html>
<head><title>Smart City Surabaya</title></head>
<body style="text-align:center;font-family:Arial">
  <h1>Portal Smart City Surabaya</h1>
  <h3>Kelompok: [NAMA KELOMPOK]</h3>
  <p>Anggota: [NAMA 1], [NAMA 2], [NAMA 3]</p>
</body>
</html>
```

**3. FTP (Server DC, Services → FTP)**

| Username | Password | Permission |
|---|---|---|
| `cctv` | `cctv123` | Write, Read, Delete, Rename, List |

**4. Email (Server DC, Services → EMAIL)**

SMTP dan POP3 diaktifkan, Domain Name `k6.smartcity.lab`.

| User | Password | Alamat email | Dipakai di |
|---|---|---|---|
| `pegawai` | `pegawai123` | `pegawai@k6.smartcity.lab` | PC Pegawai Balai Kota |
| `pelabuhan` | `pelabuhan123` | `pelabuhan@k6.smartcity.lab` | PC Kantor Pelabuhan |

Incoming dan Outgoing Mail Server di kedua PC: `mail.k6.smartcity.lab`.

---

## 4. Bukti pengujian mandiri


### Soal 1 — Perancangan topologi
![Topologi](screenshot/1-uji-topologi.png)

### Soal 2 — Tabel subnetting
![subnetting](screenshot/2-uji-subnetting.png)
[Tabel subnetting (Google Sheets)](https://docs.google.com/spreadsheets/d/1wluLNMTssKU8n0GUz-IAtyvZYIcZxmRQ2G6aUJnnOV4/edit?usp=sharing)

### Soal 3 — Static routing
![router](screenshot/3-uji-router.png)
![perangkat](screenshot/3-uji-perangkat.png)

### Soal 4 — DHCP
![uji](screenshot/4-uji-1.jpeg)
![uji](screenshot/4-uji-2.jpeg)
![uji](screenshot/4-uji-3.jpeg)
![uji](screenshot/4-uji-4.jpeg)
![uji](screenshot/4-uji-5.jpeg)
![uji](screenshot/4-uji-6.jpeg)
![uji](screenshot/4-uji-7.jpeg)
![uji](screenshot/4-uji-8.jpeg)
![uji](screenshot/4-uji-9.jpeg)
![uji](screenshot/4-uji-10.jpeg)
![uji](screenshot/4-uji-11.jpeg)
![uji](screenshot/4-uji-12.jpeg)
![uji](screenshot/4-uji-13.jpeg)
![uji](screenshot/4-uji-14.jpeg)

### Soal 5 — NAT (Network Address Translation)

Seluruh host internal (`10.48.0.0/16`) dapat mengakses server `8.8.8.8` menggunakan **satu IP publik milik R-PUSAT** (`203.0.113.26`). R-ISP **tidak boleh** memiliki rute ke alamat privat internal.

### Konfigurasi (R-PUSAT)
```
access-list 1 permit 10.48.0.0 0.0.255.255
!
interface GigabitEthernet0/0
 ip nat outside
!
interface GigabitEthernet0/1
 ip nat inside
! (juga Gi0/2 dan Vlan11 - Vlan15)
!
ip nat inside source list 1 interface GigabitEthernet0/0 overload
```

**Penjelasan:**
- `access-list 1 permit 10.48.0.0 0.0.255.255` menentukan alamat yang boleh di-NAT (seluruh `10.48.0.0/16`).
- `ip nat inside` / `ip nat outside` menandai arah translasi pada tiap interface.
- `overload` membuat banyak host memakai satu IP publik (PAT).

### Pengujian

**1. Konfigurasi NAT di R-PUSAT**

![Konfigurasi NAT R-PUSAT](screenshot/5-konfigurasi-nat.jpeg)

Satu interface outside (Gi0/0), beberapa interface inside, dan perintah `overload` terlihat.

**2. Statistik NAT**

![show ip nat statistics](screenshot/5-nat-statistics.jpeg)

Menunjukkan interface outside/inside dan jumlah translasi.

**3. Akses ke 8.8.8.8 dari beberapa wilayah**

![PC Pegawai](screenshot/5-ping-pc1.jpeg)
![PC Pegawai -t](screenshot/5-pingt-pc1.jpeg)
![PC Timur](screenshot/5-ping-pc2.jpeg)
![Smartphone](screenshot/5-ping-smartphone.jpeg)

Host dari wilayah berbeda berhasil mengakses `8.8.8.8`, dengan mengambil random sampling sebanyak 3 wilayah sebagai contoh.

**4. Tabel translasi NAT**

![show ip nat translations](screenshot/5-nat-translations.jpeg)

Beberapa alamat `10.48.x.x` (inside local) dipetakan ke satu `203.0.113.26` (inside global) dengan port berbeda.

**5. Tabel routing R-ISP**

![show ip route R-ISP](screenshot/5-iproute-risp.jpeg)

R-ISP hanya memiliki route connected `8.8.8.0/24` dan `203.0.113.24/30`. **Tidak ada** rute ke `10.0.0.0/8` atau `10.48.0.0/16`, sehingga syarat bahwa R-ISP tidak mengetahui alamat privat terpenuhi.

### Kesimpulan
NAT overload di R-PUSAT berhasil menerjemahkan seluruh host internal ke satu IP publik `203.0.113.26`. Akses ke `8.8.8.8` berhasil tanpa R-ISP memiliki rute ke jaringan privat.


### Soal 6 — Tunnel GRE

| No | Pengujian | Perintah | Hasil | Status | Bukti |
|---|---|---|---|---|---|
| 1 | Tunnel source berupa antarmuka VLAN | `tunnel source Vlan13` | Ditolak. Simulator hanya menerima Ethernet, FastEthernet, GigabitEthernet, Loopback, dan Serial. | Gagal | ![](screenshot/6-invalid-vlan13.png) |
| 2 | Tunnel memakai Loopback0 | `show interface tunnel0` | `up, line protocol is up` di R-PUSAT dan R-PELABUHAN | Berhasil | ![](screenshot/6-iface-tunnel0.png) |
| 3 | Konektivitas antar-endpoint | `ping 10.48.241.62` | `5/5 berhasil` | Berhasil | ![](screenshot/6-ping-241-62.png) |
| 4 | Konektivitas melalui tunnel | `ping 10.48.241.58` | `0/5 gagal` | Gagal | ![](screenshot/6-ping-241-58.png) |

### Soal 7 — Dynamic Routing OSPF 

**1. Link L7 dan alamatnya**

![show ip interface brief R-DC](screenshot/7-interface-rdc.jpeg)
![show ip interface brief R-UTARA](screenshot/7-interface-rutara.jpeg)

L7 ber-IP `10.48.241.57` (R-DC) dan `10.48.241.58` (R-UTARA) dan berstatus up/up.

**2. Static route sudah diganti OSPF**

![show ip route ospf R-PUSAT](screenshot/7-routeospf-rpusat.jpeg)

Seluruh subnet router lain berlabel `O`. Static route yang tersisa hanya default route ke ISP di R-PUSAT.

**3. Neighbor OSPF**

![show ip ospf neighbor R-PUSAT](screenshot/7-neighbor-rpusat.jpeg)
![show ip ospf neighbor R-UTARA](screenshot/7-neighbor-rutara.jpeg)

R-PUSAT memiliki 5 neighbor FULL, R-UTARA memiliki 3 neighbor FULL (termasuk R-DC lewat L7). Status `FULL/DR` dan `FULL/BDR` normal karena link bertipe broadcast.

**4. Tidak ada pesan OSPF ke LAN pengguna**

![show ip protocols R-BARAT](screenshot/7-protocols-rbarat.jpeg)

Interface LAN (Gi3/0 dan Gi4/0) tercantum di **Passive Interface(s)**, sedangkan Gi2/0 (link ke R-PUSAT) tidak.

**5. Default route otomatis dari OSPF**

![show ip route R-BARAT](screenshot/7-iproute-barat.jpeg)
![tracert 8.8.8.8 dari Puskesmas Barat](screenshot/7-tracert-default.jpeg)

R-BARAT memiliki `O*E2 0.0.0.0/0` via `10.48.241.49` tanpa static route. `tracert 8.8.8.8` berhasil lewat R-BARAT → R-PUSAT → R-ISP.

**6. Failover otomatis (L2 putus)**

Kondisi normal:

![tracert normal](screenshot/7-failover-sebelum.jpeg)

Jalur: R-PUSAT → R-UTARA → R-PELABUHAN (3 hop).

Setelah L2 diputus (`shutdown` Gi2/0 di R-UTARA):

![L2 diputus](screenshot/7-tes-terputus.jpeg)
![tracert setelah putus](screenshot/7-failover-setelah.jpeg)

![bukti ping](screenshot/7-failover-ping.jpeg)

R-UTARA hilang dari neighbor R-PUSAT, route ke `10.48.160.0` berpindah via `10.48.241.34` (R-DC), dan jalur menjadi R-PUSAT → R-DC → R-UTARA (lewat L7) → R-PELABUHAN (4 hop). Perpindahan terjadi otomatis tanpa perubahan konfigurasi.

### Kesimpulan
Seluruh static route berhasil diganti OSPF area 0. Default route hanya dikonfigurasi di R-PUSAT dan tersebar otomatis. Interface LAN pengguna tidak mengirim pesan OSPF, dan link L7 menjadi jalur cadangan yang aktif otomatis saat link backbone putus.


### Soal 8 — Layanan Smart City

**1. Record DNS**

![Services DNS Server DC](screenshot/8-dns-records.jpeg)

Tiga record A (`www`, `ftp`, `mail`) terdaftar di Server DC.

**2. Resolusi nama dari beberapa wilayah**

![nslookup PC Pegawai](screenshot/8-nslookup-pegawai.jpeg)
![nslookup PC Puskesmas Barat](screenshot/8-nslookup-barat.jpeg)
![nslookup PC CCTV Kota](screenshot/8-nslookup-cctv.jpeg)
![nslookup PC Kantor Pelabuhan](screenshot/8-nslookup-pelabuhan.jpeg)

`nslookup www.k6.smartcity.lab` dijawab oleh `10.48.239.10` dengan hasil `10.48.241.2`, dari Balai Kota dan dari wilayah lain.

**3. Web dibuka dengan nama domain**

![Browser Puskesmas Barat](screenshot/8-web-barat.jpeg)
![Browser CCTV Kota](screenshot/8-web-cctv.jpeg)
![Browser Kantor Pelabuhan](screenshot/8-web-pelabuhan.jpeg)

`http://www.k6.smartcity.lab` dibuka dari PC Puskesmas Barat, CCTV Kota, dan Kantor Pelabuhan, menampilkan portal dengan nama kelompok. Ini membuktikan web dapat diakses dari wilayah lain dengan nama domain.

**4. FTP: akun dan upload CCTV**

![Services FTP](screenshot/8-config-ftp.jpeg)
![Membuat File](screenshot/8-membuat-file.jpeg)
![Transfer Put File](screenshot/8-transfer-file.jpeg)
![Mengecek File via CLI](screenshot/8-mengecek-file.jpeg)
![Mengecek File via Services](screenshot/8-mengecek-file1.jpeg)

Akun `cctv` dibuat di server. Dari PC LAN CCTV, login ke `ftp.k6.smartcity.lab` berhasil, `put rekaman1.txt` sukses, dan `dir` menampilkan file tersimpan di server.

**5. Email dua arah**

![Service email Pegawai](screenshot/8-svc-emailPg.jpeg)
![Service email Pelabuhan](screenshot/8-svc-emailPl.jpeg)
![Konfigurasi email Pegawai](screenshot/8-config-emailPg.jpeg)
![Konfigurasi email Pelabuhan](screenshot/8-config-emailPl.jpeg)
![Email terkirim](screenshot/8-send-email.jpeg)
![Email terkirim](screenshot/8-send-email1.jpeg)
![Email diterima](screenshot/8-receive-email.jpeg)

Email dari `pegawai@k6.smartcity.lab` diterima di PC Kantor Pelabuhan, dan balasan dari `pelabuhan@k6.smartcity.lab` diterima di PC Pegawai.

### Catatan
Layanan ini bergantung pada routing OSPF: seluruh wilayah dapat menjangkau server karena rute ke LAN server disebarkan otomatis. Karena itu konfigurasi OSPF dan layanan Smart City dikerjakan pada satu topologi yang sama.

### Kesimpulan
DNS, web, FTP, dan email berjalan dengan domain `k6.smartcity.lab`. Nama `www`, `ftp`, dan `mail` dapat di-resolve dari seluruh wilayah, portal web dapat dibuka dengan nama domain, LAN CCTV dapat mengunggah rekaman lewat FTP berakun, dan email antar pegawai Balai Kota dan Kantor Pelabuhan berfungsi dua arah.
