# Worklog

---
Task ID: 2
Agent: main (Z.ai)
Task: Menambahkan fitur Absensi Magang Online di portal Mahasiswa (check in wajib Maps/GPS + foto lokasi kerja, check out wajib isi DOP pekerjaan magang).

Work Log:
- Menambah data `MAGANG_INFO`, tipe `AbsensiRecord`, dan `RIWAYAT_ABSENSI` (4 seed riwayat) di `src/lib/prodi-data.ts`; menambah pengumuman wajib absensi magang.
- Membuat `src/components/portal/absensi-magang.tsx`: header jam realtime WIB, data penempatan magang, alur 5 langkah, syarat check in (Maps/GPS via Geolocation API + peta OpenStreetMap embed, foto lokasi via input kamera dengan kompresi canvas 720px), tombol CHECK IN terkunci sampai 2 syarat selesai, fallback "Gunakan Lokasi Demo" saat izin GPS ditolak, form DOP (judul + deskripsi min 20 karakter + kendala), CHECK OUT terkunci tanpa DOP, kartu rekap lengkap (durasi otomatis), tabel riwayat absensi dengan badge status (Tepat Waktu/Terlambat/Check In Saja), persistensi localStorage (foto hanya pada catatan terbaru).
- Mengintegrasikan ke `dashboard-view.tsx`: menu "Absensi Magang" (mahasiswa), navigasi konten Beranda/Absensi, toast untuk menu yang belum tersedia, CTA banner "Buka Absensi Magang", aksi cepat "Absen Check-In Magang", judul topbar dinamis.
- Lint bersih setelah menghapus 3 directive eslint-disable yang tidak terpakai.
- Verifikasi Agent Browser: login mahasiswa → buka absensi → GPS ditolak (error UI + fallback OK) → lokasi demo → foto via simulasi kamera → CHECK IN aktif → tercatat 08.09 WIB (Terlambat) → Check Out terkunci tanpa DOP → isi DOP → CHECK OUT → rekap lengkap + riwayat terupdate → navigasi Beranda → mobile 390px OK → tanpa error console/runtime.

Stage Summary:
- Fitur absensi magang lengkap sesuai permintaan: cek in wajib Maps + foto, cek out wajib DOP.
- Data absensi persisten di localStorage per perangkat (demo).


---
Task ID: 1
Agent: main (Z.ai)
Task: Membangun Dashboard Program Studi Perawatan Mesin — halaman Index (landing) dengan menu Profil Singkat, Visi Misi, section lainnya, dan Login Portal (Dosen & Mahasiswa).

Work Log:
- Inisialisasi environment fullstack Next.js 16 (init script berhasil).
- Membuat data terpusat `src/lib/prodi-data.ts` (profil, visi, misi, tujuan, 6 dosen, 6 fasilitas, berita, agenda, jadwal, pengumuman, akun demo).
- Membuat komponen landing: `navbar.tsx` (sticky + mobile menu), `hero.tsx` (tema industri + gear animasi + strip statistik), `profil-singkat.tsx` (narasi + kartu identitas), `visi-misi.tsx` (visi/misi/tujuan), `dosen-section.tsx`, `fasilitas-section.tsx`, `berita-section.tsx` (berita + agenda), `login-section.tsx` (2 kartu peran), `footer.tsx`.
- Membuat `login-dialog.tsx`: dialog dengan tab Dosen (NIDN) / Mahasiswa (NIM), show/hide password, validasi error, toast sukses, info akun demo.
- Membuat `dashboard-view.tsx`: dashboard berbasis peran (sidebar, statistik, tabel jadwal, aksi cepat, status nilai/KRS, pengumuman).
- Merakit semua di `src/app/page.tsx` (state session, remount dialog via key); update `layout.tsx` (metadata + lang="id"); tambah pattern CSS `.industrial-grid` & keyframes di `globals.css`.
- Fix lint: import Cog, hapus setState dalam useEffect (ganti remount key).
- Verifikasi Agent Browser: landing render OK, smooth-scroll menu, dialog login (dosen & mahasiswa), login sukses → dashboard peran, kredensial salah → pesan error, logout, responsivitas mobile (390px) & desktop (1366px), footer sticky. Tanpa error console/runtime.

Stage Summary:
- Aplikasi web Next.js siap di port 3000, satu route `/`.
- Akun demo: Dosen NIDN 0012345678/dosen123; Mahasiswa NIM 2210001/mhs123.
- Tema: industri (slate-950 + amber), tanpa warna biru/indigo, responsif mobile-first.

---
Task ID: 3
Agent: main (Z.ai)
Task: Menambahkan Portal Pembimbing Industri (Buddy Industri) — verifikasi kehadiran per minggu + approval DOP; Rekap Absensi Magang untuk Dosen; Akun Master Kepala Prodi — rekap keseluruhan + grafik analisis kehadiran + input nilai magang (eksklusif master); mahasiswa melihat kehadiran/alpa/ijin/nilai; notifikasi pengingat per peran.

Work Log:
- Update `src/lib/prodi-data.ts`: Role diperluas jadi 4 ('dosen'|'mahasiswa'|'pembimbing'|'kaprodi') + ROLE_LABEL; akun demo baru (Dosen NIDN 0023456789/dosen123 — Ir. Budi Santoso; Pembimbing PIC-001/buddy123 — Ir. Hendra Gunawan; Kaprodi NIDN 0012345678/master123 — Dr. Ir. Ahmad Fauzi); MAGANG_INFO.tambah dosenPembimbing; data baru: MAHASISWA_MAGANG (8 mahasiswa, rekap hadir/alpa/ijin/terlambat/nilai), VERIFIKASI_SEED (9 rekap mingguan), DOP_SEED (6 DOP), REKAP_MINGGUAN_PRODI (tren 8 minggu), REKAP_PERUSAHAAN (4 perusahaan), NOTIFIKASI per Role (pengingat/info/peringatan).
- Buat `src/lib/magang-store.ts`: helper localStorage (loadNilai/simpanNilai, loadApproval/simpanApprovalVerifikasi/simpanApprovalDop, predikatNilai).
- Buat `notifikasi-panel.tsx`: popover bel notifikasi per peran dengan badge unread + tombol Tandai Dibaca.
- Buat `rekap-magang.tsx` (mahasiswa): kartu hadir/alpa/izin/terlambat, progress bar persentase, kartu nilai magang (Predikat A-D) atau "Belum Dinilai", timeline verifikasi mingguan miliknya.
- Buat `verifikasi-pembimbing.tsx` (Buddy Industri): banner statistik approval, Tabs "Kehadiran Mingguan" (Setujui/Minta Revisi) dan "DOP Magang" (Setujui DOP), filter mahasiswa berdasarkan pembimbingIndustri, persistensi approval.
- Buat `rekap-dosen.tsx`: ringkasan 4 statistik + tabel mahasiswa bimbingan (hadir/alpa/izin/terlambat/%/verifikasi/nilai) read-only.
- Buat `dashboard-kaprodi.tsx`: AnalisisKehadiran (AreaChart tren mingguan, PieChart distribusi, BarChart per perusahaan — recharts), RekapKaprodi (tabel keseluruhan + badge verifikasi + nilai), InputNilaiMagang (tabel input 0-100 + Simpan per baris, toast, persist).
- Rewrite `dashboard-view.tsx`: konfigurasi MENU/AKSI/BANNER/kartu-status per 4 peran, halaman baru (rekap-magang, rekap-dosen, verifikasi, rekap-kaprodi, nilai-kaprodi), kolom kiri beranda adaptif (jadwal / daftar mahasiswa unit / grafik untuk kaprodi), topbar pakai NotifikasiPanel + avatar per peran.
- Rewrite `login-dialog.tsx`: 4 tab peran (config-driven satu form), label ID per peran, info 4 akun demo.
- Rewrite `login-section.tsx`: 4 kartu portal (amber/emerald/teal/violet) di landing.
- Lint bersih; verifikasi Agent Browser: 4 kartu portal tampil; login mahasiswa → notifikasi 4 pengingat + tandai dibaca; Rekap & Nilai (38/1/3/2, 90%, Belum Dinilai); login kaprodi → beranda 3 grafik + input nilai 88 untuk Rizky tersimpan (badge 88/A); rekap keseluruhan menampilkan verifikasi + nilai; login pembimbing → verifikasi mingguan (approve ver-1: counter 2→1) + approval DOP (dop-1 disetujui); login dosen → rekap 4 bimbingan, Rizky "Terverifikasi" + nilai 88; login mahasiswa lagi → nilai 88 Predikat A tampil; absensi magang lama tidak regresif; mobile 390px drawer OK; tanpa error console/runtime.

Stage Summary:
- 4 peran lengkap: Mahasiswa (absen + rekap + nilai), Pembimbing Industri (verifikasi mingguan + approval DOP), Dosen (rekap absensi bimbingan), Kepala Prodi (master: grafik analisis + rekap keseluruhan + satu-satunya penginput nilai).
- Nilai & approval tersimpan localStorage sehingga konsisten antar peran di browser yang sama.
- Akun demo: 0023456789/dosen123 · 2210001/mhs123 · PIC-001/buddy123 · 0012345678/master123.

---
Task ID: 4
Agent: main (Z.ai)
Task: Menambahkan kemampuan AKUN MASTER (Kepala Prodi) untuk mengelola seluruh data portal: tambah/edit/hapus dosen, buddy industri (pembimbing lapangan), mahasiswa magang, berita, pengumuman, agenda, dan fasilitas — lengkap dengan pembuatan akun login otomatis.

Work Log:
- Perluas `src/lib/prodi-data.ts`: tambah `id` pada Berita/Pengumuman/Fasilitas, interface `AgendaItem`, `PembimbingProfil` + seed `PEMBIMBING_PROFIL` (PIC-001 s.d. PIC-004, nama sinkron dengan MAHASISWA_MAGANG), `AkunPengguna`, `KATEGORI_BERITA`.
- Buat `src/lib/app-store.ts`: store zustand + persist (localStorage, skipHydration + rehydrate manual di page.tsx) berisi koleksi dosen/pembimbing/mahasiswa/berita/agenda/pengumuman/fasilitas/akun + aksi tambah/perbarui/hapus/tambahAkun/hapusAkun/perbaruiAkun/resetMaster.
- Buat `src/components/portal/crud-panel.tsx`: panel CRUD generik (tabel + pencarian + dialog form berbasis FieldDef text/textarea/number/select + AlertDialog hapus); `uid()` generator id.
- Buat `src/components/portal/kelola-orang.tsx`: KelolaDosen (akun otomatis NIDN/dosen123, proteksi hapus akun master, sinkron akun saat edit), KelolaPembimbing (akun ID/buddy123), KelolaMahasiswa (akun NIM/mhs123, select buddy & dosen dari store, nilai tetap via Input Nilai).
- Buat `src/components/portal/kelola-konten.tsx`: Tabs Berita/Pengumuman/Agenda/Fasilitas dengan hitungan live dari store; konten langsung tampil di landing.
- Update `dashboard-view.tsx`: PageKey + menu kaprodi 4 item kelola + AKSI cepat + judul halaman + render 4 halaman baru; statistik pembimbing/kaprodi & pengumuman dari store.
- Update `dashboard-kaprodi.tsx`: RekapKaprodi, InputNilaiMagang, AnalisisKehadiran (pie + agregat per perusahaan dinamis) dari store.
- Update `login-dialog.tsx` (verifikasi akun dari store → akun buatan master bisa login + catatan info), `dosen-section/berita-section/fasilitas-section` (landing dinamis dari store, key pakai id), `page.tsx` (rehydrate useEffect), `login-section.tsx` (deskripsi kartu master), `rekap-dosen/verifikasi-pembimbing/rekap-magang` (data dari store + fallback aman + guard pembagian nol).
- Lint bersih; Verifikasi Agent Browser end-to-end: login master → tambah dosen (tampil+tabel+toast kredensial) → edit jabatan tersimpan → hapus dosen uji (konfirmasi, hilang) → proteksi hapus akun master ditolak → tambah mahasiswa 2299001 → rekap keseluruhan 9 mahasiswa → input nilai 90 (A) → login mahasiswa baru (dashboard Hafiz, nilai 90 tampil) → tambah berita (tampil di landing) → tambah buddy PIC-005 → login buddy baru sukses → mobile 390px OK → 0 error console/runtime/dev.log.

Stage Summary:
- Akun master (Kepala Prodi, NIDN 0012345678/master123) kini pusat kendali: rekap + grafik + satu-satunya input nilai + KELOLA penuh dosen/buddy/mahasiswa/berita/pengumuman/agenda/fasilitas.
- Semua entitas baru otomatis mendapat akun login (password default per peran) dan langsung terhubung ke rekap, grafik, approval, dan landing page.
- Data persisten localStorage per browser (demo), key portal-pm-master-v1.
