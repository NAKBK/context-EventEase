# EventEase
### Sistem Rekomendasi Kecocokan dan Verifikasi Aksesibilitas Fisik untuk Penyelenggaraan Event di Indonesia

**Tim NAKBK**
1. Alya Nabilla Khamil
2. Keisha Vania Laurent
3. Lessyarta Kamali Sopamena Pirade
4. Nadia Aisyah Fazila
5. Nisrina Alya Nabilah

> *"Access Made Personal, Participation Made Possible"*

---

## DAFTAR ISI

- [DAFTAR ISI](#daftar-isi) — 2
- [BAB I: Latar Belakang dan Analisis Masalah](#bab-i-latar-belakang-dan-analisis-masalah) — 3
- [BAB II: Tujuan](#bab-ii-tujuan) — 4
- [BAB III: Solusi](#bab-iii-solusi) — 5
  - [3.1 Gambaran Umum & Ruang Lingkup (MVP)](#31-gambaran-umum--ruang-lingkup-mvp) — 5
  - [3.2 Data Source](#32-data-source) — 5
  - [3.3 Metodologi Solusi](#33-metodologi-solusi) — 5
    - [3.3.1 *Need*: Memahami Kebutuhan Pengguna](#331-need-memahami-kebutuhan-pengguna) — 5
    - [3.3.2 *Match*: Mencocokkan Kebutuhan dengan Kondisi *Event*](#332-match-mencocokkan-kebutuhan-dengan-kondisi-event) — 6
    - [3.3.3 *Commitment*: Memperoleh Komitmen Penyelenggara](#333-commitment-memperoleh-komitmen-penyelenggara) — 6
    - [3.3.4 *Verify*: Membandingkan Klaim dengan Kondisi Aktual](#334-verify-membandingkan-klaim-dengan-kondisi-aktual) — 7
    - [3.3.5 *Improve*: Meningkatkan Keandalan Informasi](#335-improve-meningkatkan-keandalan-informasi) — 7
- [DAFTAR PUSTAKA](#daftar-pustaka) — 8
- [LAMPIRAN](#lampiran) — 9

---

## BAB I: Latar Belakang dan Analisis Masalah

Partisipasi dalam sebuah *event* seharusnya menjadi pengalaman yang terbuka bagi semua orang. Namun, bagi individu dengan kebutuhan mobilitas tertentu, keputusan untuk menghadiri *event* masih disertai ketidakpastian yang tinggi. Berdasarkan data Badan Pusat Statistik (BPS) tahun 2023 yang dikutip oleh Direktorat Jenderal Bina Konstruksi Kementerian Pekerjaan Umum, Indonesia memiliki sekitar 22,97 juta penyandang disabilitas, atau setara 8,5% dari total populasi nasional. Di luar kelompok tersebut, kebutuhan aksesibilitas fisik juga dapat dialami secara situasional oleh kelompok lain, seperti lansia dengan keterbatasan mobilitas, ibu hamil, Pengguna kruk pascacedera, maupun Pengguna *stroller*. Meskipun Undang-Undang Nomor 8 Tahun 2016 tentang Penyandang Disabilitas telah menjamin hak atas aksesibilitas, berbagai kasus menunjukkan bahwa pemenuhan aksesibilitas dalam Penyelenggaraan *event* publik masih menghadapi kendala dalam praktiknya.

Kasus konser Coldplay di Jakarta pada 2023 menunjukkan bagaimana ketidakjelasan informasi aksesibilitas dapat berdampak langsung pada pengalaman peserta. Seorang penonton pengguna kursi roda yang telah memiliki tiket khusus disabilitas dilaporkan mengalami kesulitan menemukan akses masuk yang sesuai karena informasi dari penyelenggara tidak konsisten dengan kondisi di lapangan (IDN Times, 2023; Liputan6.com, 2023). Persoalan serupa juga ditemukan pada penyelenggaraan konser lain, mulai dari akses *ramp* atau lift yang tidak optimal hingga minimnya informasi mengenai fasilitas bagi penyandang disabilitas sebelum acara berlangsung. Kondisi tersebut menunjukkan bahwa persoalannya bukan hanya apakah fasilitas tersedia, tetapi juga apakah pengguna dapat mengetahui, menilai, dan memastikan kesesuaiannya sebelum menghadiri *event*.

Salah satu kesenjangan utama yang terlihat dari berbagai kasus tersebut adalah informasi aksesibilitas yang masih bersifat umum, terfragmentasi, dan belum mempertimbangkan kebutuhan personal pengguna. Aksesibilitas sering direpresentasikan melalui label biner seperti "*accessible*" atau "tidak *accessible*", padahal ketersediaan fasilitas belum tentu berarti kesesuaiannya dengan kebutuhan setiap pengguna. Sebuah *venue* yang diklaim *wheelchair accessible*, misalnya, mungkin memiliki *ramp* tetapi tidak memiliki toilet aksesibel, atau memiliki jarak yang terlalu jauh antara titik *drop-off* dan lokasi acara. Informasi tersebut dapat menjadi faktor penting dalam keputusan seseorang untuk menghadiri *event*, tetapi sering kali sulit diketahui atau dikonfirmasi sebelum pengguna tiba di lokasi.

Kesenjangan antara kebutuhan personal pengguna, informasi yang disediakan penyelenggara, dan kondisi aktual di lapangan membuat pengguna sulit mengambil keputusan yang terinformasi sebelum menghadiri *event*. Kesenjangan inilah yang menjadi fokus **EventEase**. Melalui pendekatan *personalized accessibility matching*, EventEase membantu Pengguna memahami bukan sekadar apakah sebuah *event* diklaim *accessible*, tetapi apakah *event* tersebut sesuai dengan kebutuhan aksesibilitas mereka. Sistem kemudian menghubungkan hasil tersebut dengan konfirmasi dari penyelenggara dan verifikasi pengalaman aktual setelah *event*, sehingga informasi aksesibilitas dapat menjadi lebih personal, transparan, dan dapat dipercaya.

---

## BAB II: Tujuan

Berdasarkan tiga akar masalah yang telah diidentifikasi, EventEase dirancang dengan empat tujuan strategis yang saling terkait sebagai berikut.

1. **Mempersonalisasi Aksesibilitas Berbasis Profil Kebutuhan Fungsional**
   Menghapus stigma pelabelan medis dengan mengizinkan pengguna mendeskripsikan keterbatasan mobilitas secara alamiah pada profil pengguna, sehingga sistem dapat menerjemahkannya menjadi parameter teknis yang objektif, transparan, dan ramah psikologis.

2. **Menjembatani Peserta dan Penyelenggara via *Accessibility Request***
   Menghadirkan alur komunikasi dua arah yang terdokumentasi antara pengguna dan pengelola *venue* untuk memastikan komitmen penyediaan fasilitas aksesibel sebelum acara berlangsung.

3. **Optimalisasi Navigasi Ruang Berbasis Matriks Aturan (*Rule-Based Spatial Matching*)**
   Mengimplementasikan pencocokan dan pemfilteran graf digital *venue* berbasis aturan (*deterministic constraint matching*) untuk merekomendasikan rute tanpa hambatan, titik *drop-off* ideal, dan posisi duduk terbaik secara instan dan akurat.

4. **Membangun Akuntabilitas Data dan Realisasi Purwarupa Siap Pakai**
   Menciptakan siklus verifikasi fasilitas pasca-*event* untuk menjaga pembaruan data *venue* secara dinamis.

---

## BAB III: Solusi

### 3.1 Gambaran Umum & Ruang Lingkup (MVP)

Untuk memastikan solusi tetap dapat dikembangkan dalam waktu 24 jam *Hack Day*, EventEase membatasi cakupan MVP pada aspek *Mobility & Physical Access*. Fokus ini mencakup pengguna dengan kebutuhan mobilitas yang serupa, yaitu pengguna kursi roda, pengguna kruk atau dengan keterbatasan mobilitas sementara, ibu hamil, lansia, dan pengguna *stroller*. Pada tahap awal, EventEase akan diterapkan pada *event* publik seperti konser, seminar, pameran, dan festival di wilayah DKI Jakarta. Data awal terkait aksesibilitas *venue* akan memanfaatkan data yang tersedia dari pemerintah setempat.

Tujuh atribut aksesibilitas fisik yang dievaluasi, meliputi *step-free entrance*, *elevator/ramp*, *accessible restroom*, *accessible seating*, *rest area*, *accessible parking/drop-off*, *walking distance*.

### 3.2 Data Source

EventEase mengadaptasi subset atribut dari *A11yJSON*, skema data terbuka untuk mendeskripsikan aksesibilitas fisik yang dikembangkan Sozialhelden e.V., organisasi yang juga mengelola *Wheelmap* dan *accessibility.cloud*. Data awal (*seed data*) *venue* publik memanfaatkan Data Prasarana dan Sarana Aksesibilitas Penyandang Disabilitas DKI Jakarta, dilengkapi input mandiri *event organizer* saat registrasi. Untuk mengatasi sifat data *event* yang dinamis (berubah tiap penyelenggaraan), EventEase mengadopsi pendekatan *crowdsourcing* terverifikasi, mengacu pada preseden *Project Sidewalk* yang membuktikan data aksesibilitas berskala besar dapat dikumpulkan dari kontribusi pengguna dengan kontrol kualitas sederhana, mencapai akurasi identifikasi hingga 92%.

### 3.3 Metodologi Solusi

#### 3.3.1 *Need*: Memahami Kebutuhan Pengguna

Pengguna mengisi *checklist* kebutuhan berbasis fungsi (bukan diagnosis), mencakup 7 parameter: *step-free entrance*, *elevator/ramp*, *accessible restroom*, *accessible seating*, *rest area*, *accessible parking/drop-off*, *walking distance*. Tiap parameter ditandai **Required / Not Required** (*walking distance*: Short/Moderate/Tidak masalah). Parameter yang di-*Required*-kan mendapat bobot lebih tinggi pada tahap *Match*.

#### 3.3.2 *Match*: Mencocokkan Kebutuhan dengan Kondisi *Event*

Karena belum tersedia dataset *event-accessibility* Indonesia berlabel yang memadai untuk melatih model *supervised learning* dalam rentang waktu *Hack Day*, MVP EventEase menggunakan pendekatan *rule-based weighted scoring* yang memiliki landasan matematis dan preseden akademik kuat, mengadaptasi *Weighted Sum Model* (WSM), metode *multi-criteria decision analysis* yang telah luas diterapkan pada pengambilan keputusan berbasis banyak kriteria, termasuk pada studi penentuan lokasi dan prioritas fasilitas publik di Indonesia.

**Formula skor kecocokan:**

$$S = \left[ \frac{\sum (w_i \times x_i)}{\sum w_i} \right] \times 100\%$$

*(dijumlahkan untuk i = 1, 2, …, n; pada MVP n = 7 atribut)*

- **xᵢ** = tingkat pemenuhan atribut ke-i oleh penyelenggara (1 = terpenuhi, 0,5 = sebagian, 0 = tidak tersedia)
- **wᵢ** = bobot kepentingan atribut ke-i bagi pengguna tersebut (0–1), diturunkan dari *checklist* kebutuhan personal, atribut yang dicentang mendapat bobot lebih tinggi
- **n** = jumlah atribut yang dievaluasi (n = 7 pada MVP)

Bobot dasar (wᵢ) disusun melalui *pairwise comparison* mengikuti *Analytic Hierarchy Process* (AHP) (Amrullah, I., & Raflis, 2025), sebagaimana diterapkan pada studi optimalisasi fasilitas difabel tunanetra di DKI Jakarta yang menggunakan AHP untuk menentukan prioritas kriteria aksesibilitas (Saaty, T. L., 2008). Panel kecil Pengguna (kursi roda, ibu hamil, lansia) melakukan perbandingan berpasangan antar tujuh atribut sebelum *Hack Day* untuk menghasilkan bobot dasar yang valid secara metodologis; bobot tersebut kemudian dipersonalisasi ulang secara *real-time* sesuai *checklist* masing-masing pengguna.

#### 3.3.3 *Commitment*: Memperoleh Komitmen Penyelenggara

Pengguna mengajukan *Accessibility Request* yang mencakup kebutuhan aksesibilitas spesifik dan estimasi waktu kedatangan. Selanjutnya, penyelenggara memberikan respons berdasarkan kemampuan pemenuhan kebutuhan tersebut, yaitu *Can Fulfill*, *Partially Fulfill*, atau *Cannot Fulfill*. Setelah disepakati, status *Confirmed* akan tersimpan sebagai bentuk komitmen tertulis yang dapat dirujuk oleh kedua pihak. Tahap ini menjadi pembeda utama EventEase dibanding platform peta aksesibilitas umum: sistem tidak berhenti di rekomendasi berbasis data statis, tetapi membuka komunikasi langsung dan mendokumentasikan komitmen tertulis yang dapat dirujuk kedua pihak.

#### 3.3.4 *Verify*: Membandingkan Klaim dengan Kondisi Aktual

Pasca-*event*, pengguna mengisi survei singkat untuk memverifikasi tingkat pemenuhan setiap atribut aksesibilitas yang telah dijanjikan, dengan status *fulfilled*, *partially fulfilled*, atau *not fulfilled*. Hasil verifikasi tersebut kemudian digunakan untuk menghitung *Accessibility Reliability Score* bagi setiap penyelenggara berdasarkan rata-rata bergerak dari riwayat verifikasi. Skor ini ditampilkan pada profil penyelenggara sebagai informasi bagi pengguna dalam memilih *event* berikutnya. Dengan demikian, EventEase menutup siklus *Need → Match → Commit → Verify → Improve* dan memastikan informasi aksesibilitas terus diperbarui berdasarkan pengalaman aktual pengguna.

#### 3.3.5 *Improve*: Meningkatkan Keandalan Informasi

Hasil verifikasi dikonversi menjadi *Accessibility Reliability Score* per penyelenggara *event* (rata-rata bergerak dari histori verifikasi), ditampilkan pada profil penyelenggara untuk *event* berikutnya. Dengan demikian, data EventEase tidak bersifat statis, sistem membentuk siklus berkelanjutan. Semakin banyak *event* yang diverifikasi, semakin tinggi keandalan rekomendasi yang dapat diberikan kepada pengguna berikutnya, menutup siklus *Need → Match → Commit → Verify → Improve* dan menjadi basis data untuk *Roadmap* ML (V2).

Pendekatan *crowdsourcing* untuk pengumpulan dan audit informasi aksesibilitas ini memiliki preseden pada *Project Sidewalk*, yang menunjukkan kontribusi pengguna dapat membantu memperluas dan meningkatkan kualitas data aksesibilitas secara bertahap dalam skala besar.

---

## DAFTAR PUSTAKA

- Amrullah, I., & Raflis. (2025). Value Based Decision Pada Fasilitas Difabel Tunanetra di DKI Jakarta. *Menara: Jurnal Teknik Sipil*, 20(2), 236–248. https://doi.org/10.21009/jmenara.v20i2.53553
- IDN Times. (2023, 16 November). Curhat Penonton Konser Coldplay Disabilitas Tempatnya Diserobot Orang. https://www.idntimes.com/hype/viral/muhammad-bimo-aprilianto/curhat-penonton-konser-coldplay-disabilitas-tempatnya-diserobot-orang
- Isneini, M., & Luthfianto, F. P. (2025). "Leave No One Behind at Indonesian Construction." Direktorat Jenderal Bina Konstruksi, Kementerian Pekerjaan Umum. https://binakonstruksi.pu.go.id/publikasi/karya-tulis/leave-no-one-behind-at-indonesian-construction/
- Liputan6.com. (2023, 17 November). Sisi Lain Konser Coldplay, Pengguna Kursi Roda Sempat Dapat Pengalaman Tak Menyenangkan. https://www.liputan6.com/disabilitas/read/5455709/
- Pemerintah Republik Indonesia. (2016). Undang-Undang Nomor 8 Tahun 2016 tentang Penyandang Disabilitas.
- Saaty, T. L. (2008). Decision Making with the Analytic Hierarchy Process. *International Journal of Services Sciences*, 1(1), 83–98.

---

## LAMPIRAN

**Lampiran 1. System Architecture**

Diagram arsitektur sistem EventEase menggambarkan dua aktor utama — *Receiver* (menemukan *event* berdasarkan kebutuhan mobilitas personal, mengajukan *accessibility request*, dan memverifikasi aksesibilitas aktual) dan *Event Organizer* (mempublikasikan *event*, menyediakan informasi aksesibilitas, dan merespons *accessibility request*) — yang berinteraksi dengan **EventEase Platform**. Platform terdiri dari:
- **Web Application** (*Next.js + Tailwind CSS*): menyediakan *event discovery*, input kebutuhan aksesibilitas, hasil *matching* personal, permintaan, verifikasi, dan *dashboard* penyelenggara.
- **Application Backend** (*REST API*): menangani pengguna, *event*, profil aksesibilitas, permintaan, komitmen, verifikasi, dan logika aplikasi.
- **Needs Parser** (*LLM/NLP – Optional*): mengonversi kebutuhan mobilitas berbahasa alami menjadi atribut aksesibilitas terstruktur.
- **Accessibility Matching Engine** (*Rule-Based WSM + AHP Weights*): membandingkan kebutuhan *receiver* dengan atribut aksesibilitas penyelenggara dan menghitung skor kompatibilitas beserta penjelasannya.
- **Reliability Engine** (*Rule-Based Aggregation*): menghitung *Accessibility Reliability Score* dari riwayat verifikasi pasca-*event*.
- **EventEase Database** (*PostgreSQL*): menyimpan pengguna, *event*, profil aksesibilitas *venue*, kebutuhan fungsional, komitmen penyedia, dan riwayat verifikasi.
- **DKI Jakarta Open Data**: menyediakan data awal aksesibilitas *venue* publik.

**Lampiran 2. Personalized Accessibility Matching Algorithm Diagram**

Diagram alur algoritma pencocokan aksesibilitas personal terdiri dari enam tahap:
1. **Input 1 — Receiver Profile**: pengguna memilih kebutuhan aksesibilitas dari 7 atribut (*step-free entrance*, *elevator/ramp*, *accessible restroom*, *accessible seating*, *rest area*, *accessible parking/drop-off*, *walking distance/route difficulty*), masing-masing ditandai *Required*/*Not Required* (untuk *walking distance*: Short/Moderate/Tidak ada batasan spesifik).
2. **Input 2 — Provider/Venue Data**: informasi aksesibilitas *venue* dari penyelenggara *event*, dinormalisasi menjadi 1,0 (terpenuhi), 0,5 (sebagian terpenuhi), atau 0 (tidak tersedia). Contoh: *Step-free entrance* = 1,0; *Accessible restroom* = 0,5; *Parking/drop-off* = 0.
3. **Weight Generation**: menentukan bobot kepentingan personal melalui *AHP Pairwise Comparison* (membandingkan kepentingan tiap atribut 1–7), menghasilkan *Base Weight* untuk tiap atribut, lalu dipersonalisasi berdasarkan *checklist receiver* — bobot meningkat untuk atribut yang *required* dan menurun untuk yang tidak — menghasilkan *Personalized Weight* (wᵢ).
4. **Matching Engine**: menghitung skor kompatibilitas menggunakan *Weighted Sum Model*: S = [Σ(wᵢ × xᵢ) ⁄ Σwᵢ] × 100%, di mana wᵢ = bobot kepentingan personal, xᵢ = tingkat pemenuhan penyelenggara, n = 7 atribut aksesibilitas.
5. **Output**: menghasilkan (1) skor kompatibilitas keseluruhan (contoh: 82%), (2) rincian tingkat atribut (misalnya *Step-free entrance* fulfilled, *Elevator/ramp* fulfilled, *Accessible restroom* fulfilled, *Rest area* partially fulfilled, dari total 7 atribut), dan (3) rekomendasi yang dapat dijelaskan (*explainable recommendation*), misalnya cocok untuk kunjungan singkat, disarankan datang lebih awal untuk mengurangi waktu antre menuju fasilitas aksesibel.
6. **Spatial Matching** (*proses paralel opsional*): menemukan rute dan lokasi terbaik dalam *venue* berdasarkan batasan mobilitas *receiver* (jenis alat bantu mobilitas, toleransi jarak jalan, preferensi kesulitan rute), graf digital *venue* (simpul jalan masuk, fasilitas, kursi; sisi jalur, lorong), pemfilteran berbasis aturan (memfilter rute yang tidak sesuai, menerapkan batasan pengguna), lalu menghasilkan kandidat rute aksesibel dan rekomendasi rute/*drop-off*/tempat duduk terbaik beserta detail aksesibilitasnya.

**Lampiran 3. Data Flow Diagram**

Diagram alir data menunjukkan tiga sumber data — **External Data** (data infrastruktur aksesibilitas DKI Jakarta, skema aksesibilitas A11yJSON), **Provider Data/Event Organizer** (informasi *event*, informasi *venue*, 7 atribut aksesibilitas, dokumen pendukung, komitmen aksesibilitas), dan **Receiver Data/Event Attendee** (kebutuhan aksesibilitas fungsional, *Accessibility Request*, estimasi waktu kedatangan, verifikasi pasca-*event*) — yang mengalir ke dalam **EventEase System**, terdiri dari:
1. **External Data Processing**: normalisasi data → *Venue Accessibility Database*.
2. **Provider Data Processing**: validasi → *Venue/Event Database*.
3. **Receiver Data Processing**: profil aksesibilitas → pembuatan bobot personal → *Matching Engine*.
4. **Matching & Recommendation**: *rule-based matching*, *WSM compatibility scoring*, *explainable recommendation* → *Recommendation Output* (skor kompatibilitas keseluruhan, rincian tingkat atribut, rekomendasi kontekstual).
5. **Accessibility Request & Commitment**: *Accessibility Request* dari *Receiver*, respons Provider, status komitmen (tersimpan sebagai komitmen tertulis).
6. **Post-event Verification & Reliability**: verifikasi pasca-*event* dari *Receiver* → *Verification Database* → *Accessibility Reliability Score* → *Provider Profile* → rekomendasi masa depan yang lebih baik (*Feedback Loop*).

*Feedback Loop* menutup siklus: data *event* & *venue* → skor kompatibilitas (*Match*) → respons penyelenggara (*Commitment*) → pengalaman aktual → hasil verifikasi (*Verify*) → skor keandalan diperbarui → informasi penyelenggara diperbarui (*Improve*) → rekomendasi yang lebih baik.

**Lampiran 4. Design System Diagram**

Diagram menunjukkan interaksi antara **Receiver/Event Attendee** (mencari dan menghadiri *event* yang sesuai kebutuhan aksesibilitas: membuat/memperbarui profil aksesibilitas, memilih kebutuhan aksesibilitas, menjelajah *event* yang tersedia, memilih *event*, melihat rincian skor kompatibilitas & atribut, melihat rekomendasi rute/*drop-off*/tempat duduk aksesibel, mengajukan *Accessibility Request*, menunggu respons penyelenggara, menerima status komitmen, menghadiri *event*, mengisi verifikasi pasca-*event*, skor keandalan diperbarui) dan **Provider/Event Organizer** (membuat *event* dapat diakses semua orang: registrasi sebagai penyelenggara, membuat *event*, input informasi aksesibilitas *venue* (7 atribut), mengunggah dokumen pendukung, menerima *Accessibility Request*, meninjau kebutuhan yang diminta, merespons (*can fulfill*/*partially fulfill*/*cannot fulfill*), komitmen tersimpan, menerima verifikasi pasca-*event*, skor keandalan penyelenggara diperbarui) — keduanya terhubung melalui **EventEase System** (*Match → Explain → Commit → Verify → Improve*) yang mencakup *Data Processing & Normalization*, *Data Layer* (*Venue/Event Database*, *User Profile Database*), *Core Processing Modules* (*Accessibility Profile Service*, *Venue & Event Data Service*, *Matching & Scoring Engine*, *Explainable Recommendation Module*, *Accessibility Request & Verification Module*, *Reliability Scoring Module*, *Venue Graph/Spatial Matching Module*), dan **Core Loop** *Need → Match → Explain → Commit → Verify → Improve*.

**Lampiran 5. Use Case Diagram**

Diagram *use case* EventEase menunjukkan dua aktor:
- **Pengguna (Receiver)**: Lihat Reliability Score Penyelenggara, Lihat Skor Kecocokan (*Match*) *«includes»* Isi *Checklist* Kebutuhan (*Need*), Isi Survei Verifikasi (*Verify*) *«includes»* Ajukan *Accessibility Request* (*Commitment*) *«includes»* Registrasi *Event* & Isi Kapabilitas Atribut.
- **Penyelenggara**: Respons *Accessibility Request* (*Can/Partially/Cannot Fulfill*) → terhubung ke Registrasi *Event* & Isi Kapabilitas Atribut.

**Lampiran 6. Desain Low Fidelity**

Kumpulan *wireframe* *low-fidelity* untuk dua peran pengguna:

*Penyelenggara:*
- **Dashboard**: tombol *New Request*, *Waiting for Response*, *Active Event*; bagian *Score*; peta (*Map*); daftar *Accessibility Request* (*See All*) dengan nama pelanggan.
- **Event Saya**: tombol *+ Register* untuk *event* baru; daftar *event* *Ongoing* dan *Completed*.
- **Registrasi**: daftar item aksesibilitas dengan status (*Fulfilled*, *Partial*, *Need Document*); tombol *Save* dan *Upload Documents*.
- **Profil**: nama *venue*, skor, riwayat (*History*), dan bagian "How does it work?".

*Pengguna (Receiver):*
- **Rumah**: kolom pencarian, peta (*Map*), bagian *Top Picks For You* (*See All*) dengan kartu *event*.
- **Riwayat**: daftar *event* *Ongoing* dan *Event History* dengan tautan *Review* per item.
- **Verifikasi**: modal *Post-Event Verification* dengan daftar *checkbox* dan tombol *Verify*.
- **Permintaan**: kartu skor, daftar item dengan status (*Fulfilled*, *Partial*), tombol *Request Accessibility* dan *See Venue Details*.
- **Permintaan (Modal)**: formulir *Request Accessibility* dengan input *Arrival Estimation*, *Specific Request*, dan tombol *Send Request*.
- **Profil**: nama pelanggan, total ulasan *venue* yang diberikan, daftar *checkbox* *Mobility Issues*.