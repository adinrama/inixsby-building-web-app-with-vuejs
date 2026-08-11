# Studi Kasus Proyek — Hari 2: Vue 3 Dasar

## "TokoKu" — Fase 2: Interaksi, Kondisi, Daftar & Formulir

---

## 1. Skenario

Studi kasus ini **melanjutkan proyek `vue-tokoku` dari Hari 1**. Pemilik toko meminta empat peningkatan agar TokoKu terasa lebih hidup dan interaktif:

1. Sambutan & promo yang bisa direspons pengguna (event handling).
2. Info stok yang lebih informatif dan panel filter yang bisa dibuka-tutup (conditional rendering).
3. Produk dikelompokkan per kategori dengan nomor urut (list rendering lanjutan).
4. Formulir untuk menambahkan produk baru langsung dari halaman (form binding).

> **Prasyarat teknis:** peserta memakai project `vue-tokoku` hasil Hari 1 apa adanya (struktur folder, `daftarProduk`, `ProductCard.vue`, `StoreHeader.vue` tidak berubah kecuali disebutkan eksplisit).

---

## 2. Tujuan yang Diuji

| No | Tujuan Pembelajaran (dari silabus) | Dibuktikan di Tahap |
|----|--------------------------------------|----------------------|
| 1 | Event Handling: click, input, submit + modifier | Tahap 1 & 4 |
| 2 | Conditional Rendering: v-if/v-else-if/v-else, v-show | Tahap 2 |
| 3 | List Rendering: v-for, index, key, nested loop | Tahap 3 |
| 4 | Form Binding: v-model + modifier (.trim, .number), checkbox, radio | Tahap 4 |

---

## 3. Tugas Bertahap

### Tahap 1 — Event Handling *(±20 menit)*

1. Tambahkan **banner sambutan** di paling atas `App.vue`: `"🎉 Selamat datang di TokoKu!"` dengan tombol **"✕ Tutup"**. Gunakan state `tampilkanBanner` (`ref(true)`) dan `@click` untuk menyembunyikan banner.
2. Tambahkan tombol **"🎁 Klaim Kupon Diskon 10%"**. Gunakan `@click.once` agar fungsi klaim hanya bisa terpicu satu kali walau tombol diklik berkali-kali sebelum tampilan berubah. Setelah diklaim, ganti tampilan tombol menjadi info kode kupon (pakai `v-if`/`v-else`).
3. Tambahkan efek **hover** pada setiap kartu produk: gunakan `@mouseover` dan `@mouseleave` untuk menyimpan `id` produk yang sedang disorot ke sebuah `ref`, lalu beri class visual berbeda (`:class`) pada produk yang sedang di-hover.

**Checklist:** banner bisa ditutup, kupon hanya terklaim sekali, kartu produk bereaksi saat kursor diarahkan ke atasnya.

---

### Tahap 2 — Conditional Rendering *(±20 menit)*

1. Di `ProductCard.vue`, ubah label status stok dari 2 kondisi menjadi **3 tingkat** memakai `v-if` / `v-else-if` / `v-else`:
   - `stok === 0` → **"Stok Habis"**
   - `stok > 0 && stok <= 3` → **"Stok Menipis (n)"**
   - `stok > 3` → **"Stok: n"**
2. Tambahkan tombol **"Tampilkan/Sembunyikan Filter"** yang men-toggle sebuah panel filter berisi checkbox **"Hanya tampilkan produk yang tersedia"**. Karena panel ini akan sering dibuka-tutup, gunakan **`v-show`**, bukan `v-if`.
3. Terapkan hasil checkbox filter tersebut ke grid produk yang sudah ada (gabungkan dengan filter pencarian dari Hari 1).

**Checklist:** label stok berubah sesuai 3 kondisi; panel filter tetap ada di DOM (cek lewat DevTools) walau disembunyikan — buktikan bedanya dengan `v-if`.

---

### Tahap 3 — List Rendering Lanjutan *(±25 menit)*

1. Buat konstanta `kategoriList` berisi 3 nama kategori yang sudah dipakai di data (`Fashion`, `Elektronik`, `Makanan`).
2. Buat section baru **"Belanja per Kategori"**: gunakan **nested `v-for`** — loop luar untuk `kategoriList`, loop dalam untuk produk yang `kategori`-nya cocok.
3. Di dalam loop dalam, tampilkan **nomor urut** produk per kategori (bukan index global) menggunakan parameter kedua `v-for`: `(produk, index) in ...`.
4. Pastikan **setiap level** `v-for` memakai `:key` yang stabil (nama kategori untuk loop luar, `produk.id` untuk loop dalam).

**Checklist:** produk tampil terkelompok rapi per kategori, masing-masing kategori punya nomor urut mulai dari 1.

---

### Tahap 4 — Form Binding: Tambah Produk Baru *(±35 menit)*

1. Buat form baru di `App.vue` berjudul **"Tambah Produk Baru"** dengan field:
   - Nama produk → `<input>` teks, pakai `v-model.trim`
   - Harga → `<input type="number">`, pakai `v-model.number`
   - Stok → `<input type="number">`, pakai `v-model.number`
   - Kategori → 3 tombol radio (dari `kategoriList`), pakai `v-model`
   - Checkbox **"Tandai sebagai produk unggulan"**, pakai `v-model`
2. Tempelkan `@submit.prevent` pada `<form>` agar tidak me-reload halaman, arahkan ke method `tambahProduk`.
3. Di `tambahProduk`: validasi sederhana (nama, kategori, harga wajib diisi) — jika gagal, tampilkan pesan error dengan `v-if`. Jika lolos, `push` objek produk baru ke `daftarProduk` (reactive), lalu reset semua field form.
4. Produk baru harus langsung muncul di grid & di section "Belanja per Kategori" tanpa refresh (karena reaktif).

**Checklist:** submit form dengan field kosong menampilkan error dan tidak menambah data; submit valid menambah produk baru dan mengosongkan form.

---

## 4. Kriteria Penilaian

| Aspek | Bobot |
|---|---|
| Event Handling (@click, @click.once, @mouseover/@mouseleave, @submit.prevent) | 25 |
| Conditional Rendering (v-if/v-else-if/v-else 3 tingkat, v-show) | 25 |
| List Rendering (nested v-for, index, :key konsisten) | 25 |
| Form Binding (v-model.trim, v-model.number, radio, checkbox, validasi) | 25 |
| **Total** | **100** |

---
