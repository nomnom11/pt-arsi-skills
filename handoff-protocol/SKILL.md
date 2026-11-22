---
name: handoff_protocol
description: "Template Pesan Serah-Terima (Handoff Protocol) standar internal PT.ARSI.Tbk (EL, 2026-11-21): 6 format wajib untuk perpindahan tugas antar-meja (Penjaga Terjadwal→Arsi, Arsi→Peneliti, Arsi→Builder, Arsi→Tukang Periksa, Arsi→Builder Fase 2, Arsi→Pekerja Browser) agar konteks lengkap tanpa informasi tercecer. Dipakai Arsi (Ketua Pelaksana) setiap kali menurunkan perintah ke anggota tim untuk bounty make.fun."
---

# Handoff Protocol — PT.ARSI.Tbk (EL, 2026-11-21 23:38 WIB)

Setiap perpindahan tugas antar-meja memakai template di bawah ini, diisi lengkap (tidak ada field kosong yang relevan). Arsi (Ketua Pelaksana) adalah pengirim/penegak format ini.

**Gerbang yang tetap berlaku di atas semua template (tidak boleh dilompati):**
- Submit hanya setelah izin eksplisit EL (gerbang submit SOP).
- Setiap penandatanganan/klaim mengikuti On-Chain Safety Rails: ringkasan satu baris + konfirmasi eksplisit EL sebelum signing.
- Setiap tahap yang memproses konten kit/repo unduhan bisa memicu pemberitahuan keamanan yang butuh izin segar EL.

## 1. Handoff: Penjaga Terjadwal → Arsi (Laporan Target Bounty)

Dipakai saat Penjaga Terjadwal menemukan bounty bernilai EV tinggi.

```
[HANDOFF: PENJAGA TERJADWAL -> ARSI]
- Bounty ID / URL: <url>
- Kategori & Hadiah: <kategori> | Total <jumlah> USDC (Paid Places: Top <X>)
- Deadline: <sisa waktu>
- Tingkat Persaingan: <jumlah entri valid saat ini> | Skor Top-1: <skor>
- Alasan Layak Eksekusi: <misal: baru rilis, memiliki local grader, hambatan teknis tinggi>
- Status: Menunggu persetujuan Arsi untuk menurunkan Peneliti.
```

## 2. Handoff: Arsi → Peneliti (Perintah Bedah Aturan & Grader)

Dipakai Arsi begitu bounty disetujui.

```
[TASK ORDER: ARSI -> PENELITI]
- Target Bounty: <nama_bounty> (<url>)
- Direktori Kerja: <path_folder>
- Tugas Wajib:
  1. Unduh kit bounty (jika belum) dan baca seluruh file aturan (`README.md`, `skill.md`, skrip local grader).
  2. Ekstrak fakta objektif: rumus skor pasti, tie-breaker, batas runtime/memori, library yang diizinkan, dan batasan offline sandbox.
  3. Identifikasi celah paling krusial yang membedakan entri sekadar lolos dengan skor Juara 1.
- Output yang Diminta: Kirim kembali [LAPORAN BEDAH GRADER] ke Arsi. Jangan menulis kode solusi.
```

## 3. Handoff: Arsi → Builder (Perintah Eksekusi Multi-Hypothesis)

Dipakai Arsi setelah menerima laporan dari Peneliti.

```
[TASK ORDER: ARSI -> BUILDER]
- Target Bounty: <nama_bounty> | Direktori: <path_folder>
- Ringkasan Metrik & Batasan (dari Peneliti):
  * Metrik Utama: <rumus_skor>
  * Batas Sandbox: Runtime <X> detik | Memori <Y> MB | Offline: Ya/Tidak
- Instruksi Eksekusi (Dilarang berhenti di 1 solusi):
  1. Bangun dan uji 3 pendekatan berbeda secara fundamental (Pendekatan A: cepat/heuristic, Pendekatan B: eksak/struktural, Pendekatan C: lanjutan/hybrid).
  2. Jalankan local grader pada ketiganya, catat skor & runtime masing-masing, dan simpan perbandingannya.
  3. Pilih arsitektur pemenang (atau gabungkan jadi Hybrid) sebagai branch utama.
- Syarat Teknis: Relative path saja, dependensi terkunci, dan jaga keutuhan log sesi untuk bundling `--muse-usage`.
- Output yang Diminta: Laporan perbandingan skor A, B, C + lokasi file solusi utama untuk diaudit Tukang Periksa.
```

## 4. Handoff: Arsi → Tukang Periksa (Perintah Adversarial QA)

Dipakai Arsi setelah Builder menyelesaikan arsitektur pemenang.

```
[TASK ORDER: ARSI -> TUKANG PERIKSA]
- Target Audit: Solusi milik Builder di <path_folder>
- Skor Klaim Builder (Local Grader): <skor_builder> (<runtime> ms)
- Tugas Audit Independen (Wajib Bukti, Dilarang Edit Kode Builder):
  1. Buat generator data uji sintetik ekstrem (input kosong, nilai batas ekstrem, noise berat/out-of-distribution, dan ukuran input maksimal).
  2. Uji kode Builder terhadap data sintetik tersebut dan cek apakah terjadi crash, timeout, memory leak, atau penurunan skor drastis (overfitting).
  3. Uji determinisme: jalankan kode 2x berturut-turut untuk memastikan output dan skornya konsisten.
- Output yang Diminta: Kirim [VERDICT QA: LULUS / GAGAL] beserta bukti log error dan tabel perbandingan skor ke Arsi.
```

## 5. Handoff: Arsi → Builder (Fase 2: Perbaikan & Micro-Optimization)

Dipakai Arsi untuk mengembalikan temuan Tukang Periksa sekaligus memicu tuning akhir.

```
[REVISION & TUNING ORDER: ARSI -> BUILDER]
- Status Audit Tukang Periksa: <LULUS / GAGAL pada poin X>
- Lampiran Temuan QA: <tempelkan ringkasan log error / edge case dari Tukang Periksa>
- Instruksi Wajib:
  1. Perbaiki seluruh celah dari Tukang Periksa menggunakan logika yang general (bukan hardcode).
  2. Jalankan Profiler (`cProfile` atau setara) untuk memangkas fungsi yang paling lambat.
  3. Jalankan skrip Parameter Sweep (Grid/Random Search) terhadap local grader untuk memaksimalkan skor hingga batas optimal.
  4. Bersihkan file sementara, pastikan lolos local grader 2x berturut-turut, dan jalankan perintah `bundle` make.fun (sertakan `--muse-usage`).
- Output yang Diminta: Skor akhir final + konfirmasi bundle siap unggah.
```

## 6. Handoff: Arsi → Pekerja Browser (Eksekusi Submit & Klaim)

Dipakai Arsi di tahap akhir untuk penyelesaian di website make.fun.

```
[DEPLOYMENT ORDER: ARSI -> PEKERJA BROWSER]
- Target Bounty: <url_bounty>
- Artefak Siap Kirim: <path_repo_github_atau_source_bundle>
- Tugas Eksekusi:
  1. Login ke akun make.fun dan navigasi ke halaman submit bounty terkait.
  2. Unggah/submit commit repository atau source bundle beserta transkrip & usage metadata.
  3. Verifikasi bahwa automatic check pada dashboard make.fun berstatus PASS (jika ada error check, segera laporkan pesan errornya ke Arsi).
  4. Pantau posisi leaderboard setelah grading selesai dan klaim reward USDC ke wallet Solana jika masuk paid places.
```

Catatan penegakan (Arsi): langkah 2 template 6 hanya dijalankan setelah izin submit eksplisit EL; langkah 4 (klaim) hanya setelah konfirmasi EL sesuai On-Chain Safety Rails. Dalam praktik saat ini submit/klaim dijalankan sesi utama via kit CLI; template ini tetap menjadi format perintahnya siapa pun eksekutornya.
