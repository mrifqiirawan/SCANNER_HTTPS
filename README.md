# Scanner HTTPS pendamping

## Mengapa ada folder ini?

Google menjelaskan bahwa HTML Service dapat membatasi getUserMedia. Apps Script tidak dapat menghapus kebijakan kamera milik frame Google. Halaman ini menempatkan kamera pada halaman HTTPS utama, sementara login dan data tetap melalui Apps Script.

Ini aplikasi web statis yang sama, bukan database baru, APK, server Node, atau jalan untuk melewati izin kamera. Izin kamera pertama tetap diberikan guru. Jangan menaruh halaman scanner di iframe Google Sites untuk pemindaian.

## Pemasangan

1. Selesaikan upgrade SOURCE, jalankan setupApplication, dan deploy versi baru.
2. Buka config.js. Isi window.REEV_GAS_URL dengan URL `https://script.google.com/macros/s/ID_DEPLOYMENT/exec` yang benar. URL deployment bukan password.
3. Letakkan index.html dan config.js pada hosting HTTPS yang sekolah kelola. Gunakan alamat final tanpa redirect lintas domain, misalnya `https://presensi.sekolah.sch.id/`. Paket ini tidak memasang hosting secara otomatis.
4. Masuk sebagai Admin pada aplikasi GAS `/exec`. Pengaturan → URL halaman scanner HTTPS → isi alamat tersebut. URL harus HTTPS tanpa query atau fragment. Simpan.
5. Buka alamat scanner langsung, bukan melalui browser bawaan WhatsApp/Instagram dan bukan embed. Gunakan Chrome Android atau Safari iOS.
6. Login guru. Saat izin kamera muncul, izinkan. Guru langsung membuka Scan QR. Uji satu kartu dan cocokkan rekap di Admin.
7. Tambahkan alamat scanner ke layar utama. Android: menu ⋮ → Tambahkan ke layar utama. iOS: Bagikan di Safari → Tambahkan ke Layar Utama.

Halaman menunggu penghubung Google maksimal 45 detik. Jika gagal, periksa deployment `/exec`, akses Web App dan scannerUrl di Pengaturan. Akses Web App mengikuti kebijakan Workspace, mungkin memerlukan otorisasi Google pada lapisan hosting. Halaman tidak dapat melewati kebijakan akun. Cookie pihak ketiga, Content-Security-Policy hosting dan pemblokiran iframe juga harus diperiksa pada perangkat nyata. Jika hosting menetapkan frame-src, izinkan domain deployment Google sesuai kebijakan sekolah. Jangan memakai isolasi lintas origin yang memutus iframe penghubung tanpa pengujian.

## Cara kerja dan batas

- config.js hanya berisi URL deployment. Tidak ada API key WA di file ini.
- iframe penghubung hanya menerima origin scanner yang Admin simpan, sumber window.top, nonce acak per halaman dan daftar metode yang diizinkan.
- Sisi scanner memeriksa hostname sandbox Google dan nonce, kemudian mengunci pasangan window/origin penerima. Pesan yang berisi token tidak dikirim dengan targetOrigin `*`.
- Server tetap memeriksa login dan hak akses setiap operasi. Token tidak ada dalam URL.
- Decoder jsQR disertakan lokal. QR siswa tidak dikirim ke layanan pembuat QR pihak ketiga. Video tidak diunggah atau disimpan.
- Scan dibatasi resolusi kerja sekitar 640 piksel dan jeda 300 ms, dengan penahan scan berulang. Server tetap berwenang menolak duplikasi.
- Kamera berhenti saat pindah menu, keluar, halaman disembunyikan atau tab ditutup. Setelah kembali dari aplikasi lain, tekan Aktifkan kembali kamera.
- Aplikasi membutuhkan internet. Tidak ada antrean presensi offline. Setelah timeout, scan ulang aman karena server menolak catatan ganda.

`index.html` dihasilkan dari SOURCE final dengan `python tools/build.py`. Jika SOURCE diperbarui, hasilkan ulang dan pasang kembali halaman scanner agar frontend dan backend seversi.

## Status uji

Pemeriksaan JavaScript, alur handshake VM, filter origin/nonce/metode dan lifecycle kamera simulasi sudah dijalankan. Koneksi iframe Google nyata, Android Chrome, Safari iOS, Add Home Screen dan kamera fisik belum dijalankan. Lakukan LIVE_ACCEPTANCE sebelum memakai secara massal.
