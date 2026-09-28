# Dokumen Teknis Modul 1 — Lingkungan Pengembangan, Git, dan Lalu Lintas HTTP

Nama/NIM: **[Ahmad haikal Harahap 105224002]**  
Repositori: **https://github.com/AhmadHaikalHarahap/Pemweb-modul1**

## 1. Lingkungan Pengembangan

Proyek dikembangkan menggunakan Next.js dengan TypeScript dan Tailwind CSS.

| Komponen | Versi/Keterangan |
|---|---|
| Sistem Operasi | Windows [lengkapi Windows 10/11] |
| Node.js | v24.19.0 |
| npm | 11.7.0 |
| Git | 2.55.0.windows.2 |
| Visual Studio Code | [lengkapi versi VS Code] |
| Next.js | 16.3.6 |
| React | 19.2.8 |
| TypeScript | ^5 |
| Tailwind CSS | ^4 |

Bukti versi Node.js, npm, dan Git terdapat pada `docs/praktikum/version2 dari npm, git dll.png`.

Proyek menggunakan Next.js App Router. Halaman utama berada pada `app/page.tsx`, sedangkan konfigurasi proyek terdapat pada `package.json`, `tsconfig.json`, dan konfigurasi Next.js.

Cara menjalankan proyek:

```bash
npm install
npm run dev
```

Kemudian aplikasi dapat diakses melalui:

```text
http://localhost:3000
```

## 2. Alur Kerja Git

Git digunakan untuk mencatat perubahan kode dan menghubungkan repositori lokal dengan GitHub.

Perintah utama yang digunakan:

```bash
git status
git add .
git commit -m "pesan commit"
git push
git pull
git switch -c nama-branch
git merge nama-branch
```

### Riwayat commit

Hasil `git log --oneline --graph` saat ini:

```text
* de5a424 (HEAD -> main, origin/main) feat: initialize Next.js project with TypeScript and Tailwind CSS
```

Untuk dokumentasi saat ini digunakan satu commit utama yang sudah tersimpan pada branch `main` dan tersinkron dengan `origin/main`.

### Pull Request

Repositori GitHub:

**https://github.com/AhmadHaikalHarahap/Pemweb-modul1**

Status saat ini belum memenuhi checkpoint Pull Request karena repositori masih menunjukkan satu commit dan belum memiliki Pull Request. Setelah perubahan dibuat pada branch baru, Pull Request perlu dibuat dan di-merge ke `main`.

**Link Pull Request:** [ISI SETELAH PR DIBUAT]

### Merge Branch

Proses merge branch telah dilakukan dan berhasil tanpa konflik.

Hasil merge:
- branch berhasil digabungkan ke `main`;
- tidak terdapat conflict yang harus diselesaikan;
- proses merge selesai dengan sukses.

Karena tidak terjadi konflik, tidak ada bagian `<<<<<<<`, `=======`, atau `>>>>>>>` yang perlu diselesaikan.

## 3. Pengamatan Lalu Lintas HTTP

Pengamatan dilakukan menggunakan Chrome DevTools dan `curl`.

### 3.1 Request halaman utama

| No | URL | Metode | Kode Status | Content-Type | Header lain |
|---|---|---|---|---|---|
| 1 | `http://localhost:3000/` | GET | 200 OK | `text/html; charset=utf-8` | `Cache-Control: no-cache, must-revalidate`, `Content-Encoding: gzip`, `X-Powered-By: Next.js` |

Pada screenshot `docs/praktikum/200.png`, request halaman utama menggunakan metode **GET** dan memperoleh status **200 OK**. Remote Address terlihat sebagai `[::1]:3000`, yang menunjukkan aplikasi berjalan pada alamat loopback IPv6.

### 3.2 Request status 404

| No | URL | Metode | Kode Status | Content-Type | Header lain |
|---|---|---|---|---|---|
| 2 | `http://localhost:3000/halaman-tidak-ada` | GET | **404 Not Found — hasil yang diharapkan** | `text/html` / sesuai hasil DevTools | Sesuai hasil DevTools |

URL tersebut merupakan URL yang diminta modul untuk menguji resource yang tidak ditemukan. Hasil pengujian menunjukkan resource yang tidak tersedia dikembalikan sebagai **404 Not Found**.

### 3.3 Resource CSS/JS dan cache

| No | URL | Metode | Kode Status | Content-Type | Header lain |
|---|---|---|---|---|---|
| 3 | Resource JavaScript localhost | GET | 304 Not Modified | Sesuai resource JavaScript | `ETag`, `If-Modified-Since`, `Last-Modified` |

Pada screenshot `docs/praktikum/304.png`, browser meminta resource JavaScript dari localhost dan menerima **304 Not Modified**. Request memiliki header seperti `If-Modified-Since` dan `If-None-Match`, sedangkan respons memiliki `ETag` dan `Last-Modified`.

Status 304 menunjukkan browser memvalidasi resource yang sudah tersimpan di cache dan server menyatakan bahwa resource tersebut belum berubah, sehingga isi resource tidak perlu dikirim ulang.

### 3.4 Request `http://github.com` melalui curl

Perintah yang digunakan:

```bash
curl.exe -I http://github.com
```

Hasil pengamatan:

```text
HTTP/1.1 301 Moved Permanently
Content-Length: 0
Location: https://github.com/
```

| No | URL | Metode | Kode Status | Content-Type | Header lain |
|---|---|---|---|---|---|
| 4 | `http://github.com` | HEAD | 301 Moved Permanently | Tidak ditampilkan | `Location: https://github.com/`, `Content-Length: 0` |

Perintah `curl -I` menggunakan metode **HEAD**, sehingga hanya header respons yang ditampilkan tanpa body. Status **301 Moved Permanently** menunjukkan bahwa alamat HTTP mengarahkan klien ke alamat HTTPS melalui header `Location`.

### 3.5 Pengamatan `https://example.com` melalui curl

Perintah yang digunakan:

```bash
curl.exe -v https://example.com
```

Hasil penting dari pengamatan:

```text
> GET / HTTP/1.1
> Host: example.com
> User-Agent: curl/8.21.0
> Accept: */*

< HTTP/1.1 200 OK
< Content-Type: text/html
< Transfer-Encoding: chunked
< Connection: keep-alive
< Server: cloudflare
< last-modified: Sat, 26 Sep 2026 09:09:07 GMT
< allow: GET, HEAD
< Accept-Ranges: bytes
< Age: 3
< cf-cache-status: HIT
```

| No | URL | Metode | Kode Status | Content-Type | Header lain |
|---|---|---|---|---|---|
| 5 | `https://example.com` | GET | 200 OK | `text/html` | `Last-Modified`, `Allow`, `Accept-Ranges`, `Age`, `cf-cache-status: HIT`, `Server: cloudflare` |

Opsi `-v` menampilkan baris request yang diawali `>` dan baris response yang diawali `<`. Berbeda dengan `curl -I`, perintah ini mengirim **GET** dan menampilkan body respons.

### 3.6 Perbandingan cache pada `developer.mozilla.org`

Modul meminta pengamatan halaman `https://developer.mozilla.org` dengan cache aktif dan nonaktif melalui Chrome DevTools. Bukti khusus untuk pengamatan tersebut belum tersedia pada data yang dikumpulkan saat ini.

Yang perlu dicatat dari DevTools adalah:
- metode request;
- kode status;
- ukuran resource;
- apakah resource berasal dari network, memory cache, disk cache, atau divalidasi dengan 304;
- perbedaan hasil ketika **Disable cache** aktif dan tidak aktif.

**Jangan mengisi angka/status dari bagian ini tanpa melakukan pengamatan langsung.**

### 3.7 Request WebSocket

Screenshot `docs/praktikum/101.png` menunjukkan request:

```text
ws://localhost:3000/_next?...
```

dengan:

```text
Request Method: GET
Status Code: 101 Switching Protocols
```

Respons memiliki header seperti:

```text
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: ...
```

Status 101 menunjukkan server menyetujui perubahan protokol dari HTTP ke WebSocket. Request ini merupakan koneksi WebSocket yang digunakan oleh lingkungan pengembangan Next.js.

## 4. Kendala dan Penyelesaian

### Kendala: Memahami Alur dan Perintah Git

Kendala yang ditemui selama praktikum adalah memahami alur penggunaan Git, terutama hubungan antara repository lokal, branch, staging area, commit, dan repository remote di GitHub. Pada awalnya, beberapa perintah Git seperti `git add`, `git commit`, `git push`, `git pull`, dan proses merge masih cukup membingungkan karena setiap perintah memiliki fungsi yang berbeda dan harus dilakukan pada urutan yang tepat.

Selain itu, pengecekan status repository melalui `git status` dan membaca hasil `git log --oneline --graph` juga membutuhkan pemahaman agar dapat mengetahui apakah perubahan sudah masuk staging area, sudah dibuat menjadi commit, atau sudah tersinkron dengan repository GitHub.

### Penyelesaian

Untuk memahami proses tersebut, digunakan bantuan **ChatGPT** untuk menjelaskan fungsi masing-masing perintah Git dengan bahasa yang lebih sederhana dan memberikan contoh alur penggunaannya. Bantuan tersebut digunakan sebagai panduan untuk memahami langkah yang harus dilakukan, kemudian setiap langkah tetap dikerjakan sendiri melalui terminal PowerShell.

Setelah mendapatkan penjelasan, perintah Git dijalankan secara langsung pada repository project. Hasilnya kemudian diperiksa menggunakan `git status`, `git log --oneline --graph`, serta pengecekan repository di GitHub. Dengan cara tersebut, dapat diketahui apakah perubahan sudah berhasil dicatat, apakah branch sudah terhubung dengan repository remote, dan apakah proses merge berhasil dilakukan.

Proses merge pada project berhasil dilakukan tanpa konflik. Setelah proses selesai, hasil merge diverifikasi kembali melalui Git dan GitHub untuk memastikan branch telah berhasil digabungkan dan perubahan tetap dapat digunakan.

### Verifikasi

Solusi yang diperoleh dari bantuan AI tidak langsung dianggap benar. Verifikasi dilakukan sendiri dengan menjalankan perintah pada PowerShell dan memeriksa hasilnya pada repository GitHub. Verifikasi meliputi:

1. Menjalankan perintah Git secara langsung pada repository.
2. Memeriksa `git status` untuk mengetahui kondisi working tree.
3. Memeriksa `git log --oneline --graph` untuk melihat riwayat commit.
4. Memastikan repository lokal tersinkron dengan `origin/main`.
5. Memeriksa repository GitHub untuk memastikan perubahan berhasil tersimpan.
6. Memastikan proses merge berhasil dan project tetap dapat dijalankan.

Dengan demikian, ChatGPT digunakan sebagai bantuan untuk memahami masalah Git, sedangkan kebenaran solusi tetap diverifikasi melalui praktik langsung oleh mahasiswa.

## 5. Catatan Pemanfaatan AI

AI digunakan sebagai alat bantu selama proses pengembangan project dan pengerjaan praktikum. Penggunaan AI difokuskan pada dua kebutuhan utama, yaitu pembuatan interface web dan penyelesaian masalah Git. Hasil dari AI tetap diperiksa dan diverifikasi secara langsung oleh mahasiswa.

### 5.1 Antigravity

**Antigravity** digunakan untuk membantu membuat interface web pada project. Bantuan digunakan dalam menyusun tampilan halaman, struktur komponen interface, serta pengembangan tampilan agar sesuai dengan konsep project yang dibuat.

Setelah interface dibuat dengan bantuan Antigravity, hasilnya diperiksa dan dijalankan secara lokal untuk memastikan tampilan dapat berjalan dengan baik dan tidak menimbulkan error pada project.

### 5.2 ChatGPT

**ChatGPT** digunakan untuk membantu menyelesaikan masalah yang berkaitan dengan Git. Bantuan meliputi pemahaman terhadap fungsi dan urutan penggunaan perintah seperti `git status`, `git add`, `git commit`, `git push`, `git pull`, branch, dan merge.

ChatGPT juga digunakan untuk membantu memahami pesan atau kondisi yang muncul pada terminal ketika melakukan proses Git. Setelah mendapatkan penjelasan, perintah tetap dijalankan sendiri oleh mahasiswa melalui PowerShell. Hasilnya kemudian diverifikasi menggunakan status repository, riwayat commit, dan repository GitHub.

### 5.3 Verifikasi Hasil AI

AI digunakan sebagai **asisten pembelajaran dan pengembangan**, bukan sebagai pengganti proses pengerjaan dan verifikasi. Setiap saran yang digunakan diperiksa kembali melalui project secara langsung. Untuk interface, hasilnya diuji dengan menjalankan aplikasi secara lokal. Untuk Git, hasilnya diperiksa melalui PowerShell dan GitHub. Dengan demikian, hasil akhir tetap berada dalam kendali dan verifikasi mahasiswa.

