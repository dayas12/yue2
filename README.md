# YuE2 Local Music Studio — frontend Vercel

Folder ini berisi tampilan web statis. Python, model, token, lirik, log, dan hasil audio berada di PC pada A:\YUE2, terpisah dari folder deploy ini.

## Pakai langsung tanpa internet

1. Jalankan A:\YUE2\Mulai-Web.bat.
2. Browser membuka http://127.0.0.1:8765 dan otomatis terhubung.
3. Isi judul, gaya, dan lirik, lalu klik Buat musik.
4. Pilih lagu dalam pustaka untuk mendengarkan, mengunduh WAV, atau memakai liriknya kembali.
5. Hentikan-Web.bat mematikan layanan lokal bila sudah selesai.

Semua proses generasi memakai RTX 3060 lokal. Layanan hanya mendengarkan 127.0.0.1. Satu lagu diproses pada satu waktu. Tidak diperlukan instalasi paket tambahan untuk server web.

## Deploy ke Vercel

Deploy HANYA folder A:\YUE2\web\frontend, atau gunakan YuE2-Vercel.zip yang berada di A:\YUE2\web.

Pilihan GitHub:
1. Buat repositori berisi isi folder frontend ini (public/, vercel.json, README.md, .vercelignore).
2. Import repositori tersebut di Vercel.
3. Framework Preset: Other. Output Directory: public. Build Command dan Install Command: kosong (tanpa build).
4. Deploy. Tidak perlu environment variable atau API key di Vercel.

Pilihan CLI jika Node.js/npm sudah tersedia:

    cd /d A:\YUE2\web\frontend
    npx --cache A:\YUE2\cache\npm vercel --prod

Vercel meminta login dan pemilihan akun/proyek. Source ini belum dipublikasikan oleh Codex.

## Menghubungkan halaman Vercel ke PC

1. Jalankan Mulai-Web.bat di PC ini.
2. Jalankan Hubungkan-Vercel.bat, masukkan URL origin hasil deploy, misalnya https://nama-studio.vercel.app (tanpa path).
3. Jalankan Token-Web.bat. Salin token dari file lokal yang terbuka.
4. Buka halaman Vercel pada PC yang sama, tempel token di Pengaturan koneksi, lalu Hubungkan.
5. Izinkan akses jaringan lokal bila Chrome/Edge memintanya.

Setiap URL preview Vercel dan domain custom yang berbeda harus didaftarkan sendiri melalui Hubungkan-Vercel.bat. Daftar domain tersimpan di web/private/allowed-origins.json.

Vercel hanya mengirim HTML, CSS, JavaScript ke browser. Browser menghubungi http://127.0.0.1:8765 secara langsung. Tidak ada Vercel Function, proxy, analytics, atau unggahan audio/model. Token ada dalam memori tab browser dan tidak disimpan di localStorage/source/Vercel. Draf lirik disimpan di localStorage browser ini; riwayat generasi disimpan pada disk PC.

Halaman Vercel membutuhkan internet untuk pertama kali dimuat. Untuk pemakaian benar-benar offline gunakan halaman localhost. Membuka halaman Vercel dari HP/PC lain tidak akan terhubung ke PC ini: localhost selalu berarti perangkat yang membuka halaman. Browser yang memblokir koneksi HTTPS ke layanan lokal dapat memakai mode localhost; Chrome/Edge dengan izin Local Network Access adalah jalur yang disarankan.

## File lokal

- A:\YUE2\web\backend.py: server lokal.
- A:\YUE2\web\private: token dan izin domain (tidak untuk di-deploy).
- A:\YUE2\web\jobs: riwayat, request, dan konfigurasi per lagu.
- A:\YUE2\web\logs: log server dan generasi.
- A:\YUE2\output\web-<id>\song: FLAC, WAV setelah diminta, skor ABC, token, dan metadata.
- A:\YUE2\models: model yang sudah diunduh.

Gunakan [Verse], [Chorus], [Bridge] pada lirik. Batas panjang adalah jumlah token, bukan target durasi. Lagu bisa selesai lebih cepat, atau terpotong ketika batas tercapai; antarmuka menampilkan keterangannya. Pilihan ekstra memakai lebih banyak VRAM. Konfigurasi menggunakan torch-eager, offload AR, budget 12 GiB, dan 32 langkah untuk kualitas standar. Seed yang sama tidak menjamin hasil identik pada perangkat/runtime berbeda.

## Rujukan

- Vercel static configuration: https://vercel.com/docs/project-configuration/vercel-json
- Vercel static builds: https://vercel.com/docs/builds/configure-a-build
- Chrome Local Network Access: https://developer.chrome.com/blog/local-network-access
- YuE2 resmi: https://github.com/multimodal-art-projection/YuE


## Fitur musik tambahan

- Buat lagu: gaya + lirik, dengan perencanaan melodi/akor atau generasi langsung.
- Cover: audio referensi atau ABC, lirik dan aransemen baru. Audio ditranskripsikan ke melodi terlebih dahulu; YuE2 tidak memakai identitas vokal dari waveform sebagai conditioning.
- Instrumental: komposisi baru atau melodi referensi/ABC. Not vokal dipindahkan ke suara instrumen; tetap dengarkan hasil untuk memeriksa kebocoran vokal.
- Edit: ambil ABC dari pustaka, ubah notasi, lirik, gaya, atau tempo (30–240 BPM), kemudian render versi baru.
- Transkripsi: audio lokal ke ABC/MIDI; pilihan menyertakan akor. Lirik tidak ditranskripsikan otomatis.
- Export ABC, MIDI untuk hasil transkripsi, dan WAV/FLAC untuk hasil audio.

Audio referensi menerima WAV/MP3/FLAC/OGG sampai 150 MB dan 20 menit. Pemrosesan memakai potongan 15/30/60/120 detik mulai dari detik yang dipilih; file asli tetap disimpan utuh. Transkripsi dan generasi memakai GPU secara berurutan. Periksa notasi dan peringatan transkripsi sebelum menganggap melodinya akurat.

Kemiripan melodi berbeda dari kemiripan suara penyanyi. Cover tidak menjamin timbre, identitas vokal, atau waveform sama dengan rekaman sumber. Editing membuat rekaman lengkap yang baru. Tidak ada fitur kloning identitas vokal di versi ini.

Transkripsi audio memakai SheetSage2 dan MERT-v2-FullSong di A:\YUE2\models. Paket Transformers transkripsi terisolasi di A:\YUE2\sheetsage-packages agar versi YuE2 utama tetap utuh. Runtime lokal menggunakan Python 3.12/torch 2.10 yang sudah terpasang, berbeda dari resep resmi Python 3.10/3.11 dan torch 2.8; pengujian lokal dicatat di web/cover-feature-status.json. Pembacaan audio lewat libsndfile dan array waveform, sehingga tidak memakai decoder FFmpeg untuk format yang diterima.

Saat bobot/paket belum lengkap, web menampilkan status persiapan dan menolak proses cover audio dengan pesan yang jelas. Cover menggunakan ABC, instrumental, dan edit notasi tidak memerlukan MERT2.

Panduan cover resmi: https://github.com/multimodal-art-projection/YuE/blob/main/docs/covers.md
Batas editing: https://github.com/multimodal-art-projection/YuE/blob/main/docs/editing.md
SheetSage2: https://huggingface.co/m-a-p/SheetSage2
Contoh melody.abc berasal dari proyek YuE (kode/dokumentasi Apache-2.0).

Pantau persiapan audio lewat Status-Fitur-Cover.bat. Jeda-Fitur-Cover.bat menjeda unduhan; Lanjutkan-Fitur-Cover.bat melanjutkannya. Tahap ready di web/cover-feature-status.json hanya ditulis setelah uji audio referensi → notasi → cover berhasil.
