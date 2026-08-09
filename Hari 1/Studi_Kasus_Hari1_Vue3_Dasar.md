# Studi Kasus Proyek — Hari 1: Vue 3 Dasar

## "TokoKu" — Etalase Produk Interaktif

---

## 1. Skenario

Anda diminta membangun bagian awal dari aplikasi etalase (storefront) sederhana bernama **TokoKu** menggunakan **Vue 3 + Vite**. Fokus hari ini hanya pada tampilan produk dan interaktivitas dasar di sisi browser — **belum ada koneksi API/backend**.

Studi kasus ini dirancang sebagai proyek tunggal yang dikerjakan bertahap, sehingga peserta langsung merasakan alur: *data → template → component → reaktivitas*, sesuai roadmap pembelajaran Hari 1.

---

## 2. Tujuan yang Diuji

| No | Tujuan Pembelajaran (dari silabus) | Dibuktikan di Tahap |
|----|--------------------------------------|----------------------|
| 1 | Konsep dasar Vue & ekosistem (Node.js, NPM, Vite) | Tahap 1 |
| 2 | Persiapan project & struktur folder | Tahap 1 |
| 3 | Template syntax: interpolasi, ekspresi JS, v-bind, v-if, v-for, v-model | Tahap 2 & 4 |
| 4 | Component & Props: reusable component, kirim data parent → child | Tahap 3 |
| 5 | Reactive state: `ref()` dan `reactive()` | Tahap 4 |

---

## 3. Data Awal (wajib dipakai apa adanya)

Agar peserta fokus ke logika Vue (bukan mengarang data), gunakan array berikut sebagai titik awal:

```js
const daftarProduk = [
  { id: 1, nama: 'Kaos Polos',        kategori: 'Fashion',    harga: 75000,  stok: 12, emoji: '👕' },
  { id: 2, nama: 'Sepatu Sneakers',   kategori: 'Fashion',    harga: 350000, stok: 0,  emoji: '👟' },
  { id: 3, nama: 'Mouse Wireless',    kategori: 'Elektronik', harga: 120000, stok: 8,  emoji: '🖱️' },
  { id: 4, nama: 'Keyboard Mekanik',  kategori: 'Elektronik', harga: 450000, stok: 0,  emoji: '⌨️' },
  { id: 5, nama: 'Kopi Arabika 250gr',kategori: 'Makanan',    harga: 65000,  stok: 20, emoji: '☕' },
  { id: 6, nama: 'Coklat Batangan',   kategori: 'Makanan',    harga: 25000,  stok: 15, emoji: '🍫' }
]
```

> Catatan: gambar produk sengaja diganti **emoji** agar peserta tidak perlu mengurus import asset gambar dulu — itu di luar cakupan Hari 1.

---

## 4. Struktur Folder yang Diharapkan

```
vue-tokoku/
├─ src/
│  ├─ components/
│  │  ├─ StoreHeader.vue
│  │  └─ ProductCard.vue
│  ├─ App.vue
│  └─ main.js
└─ package.json
```

---

## 5. Tugas Bertahap

### Tahap 1 — Setup Project *(±15 menit)*

1. Buat project baru dengan `npm create vite@latest vue-tokoku` → pilih **Vue** → **JavaScript**.
2. Jalankan `npm install` lalu `npm run dev`, pastikan halaman default Vite tampil di browser.
3. Hapus isi bawaan `App.vue`, siapkan `<script setup>` kosong dan salin **Data Awal** di atas ke dalamnya sebagai `const daftarProduk`.

**Checklist:** project berjalan tanpa error di `localhost:5173`.

---

### Tahap 2 — Template Syntax *(±30 menit)*

Kerjakan semua ini **langsung di `App.vue`** dulu (belum dipecah jadi component):

1. Tampilkan judul toko `"TokoKu"` di dalam `<h1>` menggunakan interpolasi.
2. Di bawah judul, tampilkan total jumlah produk memakai ekspresi JS: `Menampilkan {{ daftarProduk.length }} produk`.
3. Gunakan `v-for` untuk menampilkan setiap produk (nama, kategori, harga, emoji) di dalam elemen `<div>` yang diberi `:key`.
4. Format harga menjadi format Rupiah menggunakan ekspresi `{{ produk.harga.toLocaleString('id-ID') }}`.
5. Gunakan `v-if` / `v-else` untuk menampilkan teks **"Stok: (jumlah)"** jika `stok > 0`, atau **"Stok Habis"** jika `stok === 0`.
6. Gunakan `v-bind:class` (boleh shorthand `:class`) untuk menambahkan class `is-empty` pada card yang stoknya habis.

**Checklist:** seluruh 6 produk tampil dengan benar, produk stok 0 terlihat berbeda secara visual.

---

### Tahap 3 — Component & Props *(±30 menit)*

1. Buat `src/components/ProductCard.vue`. Pindahkan markup satu kartu produk (poin 3–6 Tahap 2) ke component ini.
2. Definisikan props di `ProductCard.vue` menggunakan `defineProps({...})`: `nama` (String, required), `kategori` (String, default `'Umum'`), `harga` (Number, required), `stok` (Number, default `0`), `emoji` (String, default `'📦'`).
3. Di `App.vue`, import dan render `<ProductCard>` di dalam `v-for`, kirim data lewat props (`:nama`, `:harga`, dst).
4. Buat component kedua, `src/components/StoreHeader.vue`, menerima props `namaToko` (String, required) dan `tagline` (String, default bebas). Pindahkan bagian judul toko ke sini.

**Checklist:** `App.vue` tidak lagi berisi markup kartu produk secara langsung — semuanya lewat `<ProductCard>` dan `<StoreHeader>` dengan props.

---

### Tahap 4 — Reactive State *(±35 menit)*

1. Ubah `daftarProduk` dari array biasa menjadi **`reactive([...])`** (import dari `'vue'`), supaya perubahan stok bisa terpantau di UI.
2. Tambahkan state baru dengan **`ref(0)`** bernama `jumlahKeranjang`. Tampilkan nilainya di `StoreHeader` (kirim sebagai props tambahan `jumlah-keranjang`).
3. Tambahkan tombol **"+ Keranjang"** di sebelah setiap `ProductCard` (boleh diletakkan di `App.vue`, di luar component, agar tidak perlu emit event). Saat diklik: kurangi `stok` produk terkait sebanyak 1, dan tambah `jumlahKeranjang` sebanyak 1. Tombol harus `:disabled` saat `stok === 0`.
4. Tambahkan input pencarian dengan **`v-model`** bernama `kataKunci`. Gunakan untuk menyaring `v-for` sehingga hanya produk yang namanya cocok yang tampil (`.filter(...)` + `.toLowerCase().includes(...)`).
5. **(Bonus)** Tambahkan tombol "Restock" yang hanya muncul (`v-if`) saat stok = 0, yang menambah stok produk tersebut sebanyak 10 saat diklik.

**Checklist:** klik "+ Keranjang" mengurangi stok & menambah badge keranjang secara real-time; mengetik di kotak pencarian langsung menyaring daftar produk.

---

## 6. Kriteria Penilaian

| Aspek | Bobot |
|---|---|
| Setup project & struktur folder benar | 10 |
| Template syntax (interpolasi, v-bind, v-if, v-for) | 25 |
| Component & Props (2 component, props tervalidasi tipe & default) | 30 |
| Reactive state (`ref`, `reactive`, `v-model`, interaktivitas) | 25 |
| Kerapian kode & tampilan | 10 |
| **Total** | **100** |

---

## 7. Kunci Jawaban

### `src/main.js`

```js
import { createApp } from 'vue'
import App from './App.vue'
import './style.css'

createApp(App).mount('#app')
```

### `src/components/StoreHeader.vue`

```vue
<script setup>
defineProps({
  namaToko: {
    type: String,
    required: true
  },
  tagline: {
    type: String,
    default: 'Belanja mudah, harga bersahabat'
  },
  jumlahKeranjang: {
    type: Number,
    default: 0
  }
})
</script>

<template>
  <header class="store-header">
    <div>
      <h1>{{ namaToko }}</h1>
      <p>{{ tagline }}</p>
    </div>
    <div class="cart-badge">
      🛒 Keranjang: <strong>{{ jumlahKeranjang }}</strong>
    </div>
  </header>
</template>

<style scoped>
.store-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 24px;
  background-color: #0f172a;
  color: #fff;
}
.cart-badge {
  background-color: #10b981;
  padding: 8px 16px;
  border-radius: 999px;
  white-space: nowrap;
}
</style>
```

### `src/components/ProductCard.vue`

```vue
<script setup>
defineProps({
  nama: {
    type: String,
    required: true
  },
  kategori: {
    type: String,
    default: 'Umum'
  },
  harga: {
    type: Number,
    required: true
  },
  stok: {
    type: Number,
    default: 0
  },
  emoji: {
    type: String,
    default: '📦'
  }
})
</script>

<template>
  <div class="product-card" :class="{ 'is-empty': stok === 0 }">
    <div class="product-emoji">{{ emoji }}</div>
    <span class="badge">{{ kategori }}</span>
    <h3>{{ nama }}</h3>
    <p class="price">Rp{{ harga.toLocaleString('id-ID') }}</p>

    <p v-if="stok > 0" class="stock available">Stok: {{ stok }}</p>
    <p v-else class="stock empty">Stok Habis</p>
  </div>
</template>

<style scoped>
.product-card {
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  padding: 16px;
  text-align: center;
  transition: opacity 0.2s;
}
.product-card.is-empty {
  opacity: 0.55;
}
.product-emoji {
  font-size: 40px;
}
.badge {
  display: inline-block;
  background: #e0f2fe;
  color: #0369a1;
  padding: 2px 10px;
  border-radius: 999px;
  font-size: 12px;
  margin: 6px 0;
}
.price {
  font-weight: 700;
  color: #16a34a;
}
.stock.available { color: #16a34a; font-size: 13px; }
.stock.empty { color: #dc2626; font-size: 13px; }
</style>
```

### `src/App.vue`

```vue
<script setup>
import { ref, reactive } from 'vue'
import StoreHeader from './components/StoreHeader.vue'
import ProductCard from './components/ProductCard.vue'

const namaToko = 'TokoKu'

const daftarProduk = reactive([
  { id: 1, nama: 'Kaos Polos',        kategori: 'Fashion',    harga: 75000,  stok: 12, emoji: '👕' },
  { id: 2, nama: 'Sepatu Sneakers',   kategori: 'Fashion',    harga: 350000, stok: 0,  emoji: '👟' },
  { id: 3, nama: 'Mouse Wireless',    kategori: 'Elektronik', harga: 120000, stok: 8,  emoji: '🖱️' },
  { id: 4, nama: 'Keyboard Mekanik',  kategori: 'Elektronik', harga: 450000, stok: 0,  emoji: '⌨️' },
  { id: 5, nama: 'Kopi Arabika 250gr',kategori: 'Makanan',    harga: 65000,  stok: 20, emoji: '☕' },
  { id: 6, nama: 'Coklat Batangan',   kategori: 'Makanan',    harga: 25000,  stok: 15, emoji: '🍫' }
])

const kataKunci = ref('')
const jumlahKeranjang = ref(0)

function tambahKeKeranjang(produk) {
  if (produk.stok > 0) {
    produk.stok--
    jumlahKeranjang.value++
  }
}

function restock(produk) {
  produk.stok += 10
}
</script>

<template>
  <div id="app">
    <StoreHeader
      :nama-toko="namaToko"
      tagline="Belanja mudah, harga bersahabat"
      :jumlah-keranjang="jumlahKeranjang"
    />

    <div class="search-bar">
      <input
        v-model="kataKunci"
        type="text"
        placeholder="Cari produk..."
      />
    </div>

    <p class="info">
      Menampilkan
      {{ daftarProduk.filter(p => p.nama.toLowerCase().includes(kataKunci.toLowerCase())).length }}
      dari {{ daftarProduk.length }} produk
    </p>

    <div class="product-grid">
      <div
        v-for="produk in daftarProduk.filter(p => p.nama.toLowerCase().includes(kataKunci.toLowerCase()))"
        :key="produk.id"
        class="product-slot"
      >
        <ProductCard
          :nama="produk.nama"
          :kategori="produk.kategori"
          :harga="produk.harga"
          :stok="produk.stok"
          :emoji="produk.emoji"
        />

        <div class="actions">
          <button
            @click="tambahKeKeranjang(produk)"
            :disabled="produk.stok === 0"
          >
            + Keranjang
          </button>
          <button
            v-if="produk.stok === 0"
            class="restock"
            @click="restock(produk)"
          >
            Restock
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
body { font-family: sans-serif; margin: 0; background: #f8fafc; }
.search-bar { padding: 16px 24px 0; }
.search-bar input {
  width: 100%; max-width: 320px; padding: 8px 12px;
  border: 1px solid #cbd5e1; border-radius: 8px;
}
.info { padding: 8px 24px; color: #64748b; font-size: 14px; }
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
  padding: 0 24px 24px;
}
.actions { display: flex; gap: 8px; margin-top: 8px; }
.actions button {
  flex: 1; padding: 8px; border: none; border-radius: 8px;
  background: #0f172a; color: #fff; cursor: pointer;
}
.actions button:disabled { background: #cbd5e1; cursor: not-allowed; }
.actions .restock { background: #f59e0b; }
</style>
```
