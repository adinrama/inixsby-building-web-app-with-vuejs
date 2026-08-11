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

## 5. Kunci Jawaban

`main.js` dan `StoreHeader.vue` **tidak berubah** dari Hari 1.

### `src/components/ProductCard.vue` (diperbarui — status stok 3 tingkat)

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

    <p v-if="stok === 0" class="stock empty">Stok Habis</p>
    <p v-else-if="stok <= 3" class="stock low">Stok Menipis ({{ stok }})</p>
    <p v-else class="stock available">Stok: {{ stok }}</p>
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
.product-card.is-empty { opacity: 0.55; }
.product-emoji { font-size: 40px; }
.badge {
  display: inline-block;
  background: #e0f2fe;
  color: #0369a1;
  padding: 2px 10px;
  border-radius: 999px;
  font-size: 12px;
  margin: 6px 0;
}
.price { font-weight: 700; color: #16a34a; }
.stock.available { color: #16a34a; font-size: 13px; }
.stock.low { color: #d97706; font-size: 13px; font-weight: 600; }
.stock.empty { color: #dc2626; font-size: 13px; }
</style>
```

### `src/App.vue` (versi lengkap Hari 2)

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
  { id: 6, nama: 'Coklat Batangan',   kategori: 'Makanan',    harga: 25000,  stok: 2,  emoji: '🍫' }
])

const kategoriList = ['Fashion', 'Elektronik', 'Makanan']

// --- Tahap 1: Event Handling ---
const tampilkanBanner = ref(true)
const kuponDiklaim = ref(false)
const produkDisorot = ref(null)

function klaimKupon() {
  kuponDiklaim.value = true
}

// --- Tahap 2: Conditional Rendering ---
const tampilkanFilter = ref(false)
const hanyaStokTersedia = ref(false)

// --- pencarian (dari Hari 1) ---
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

// --- Tahap 4: Form Binding ---
const formBaru = reactive({
  nama: '',
  harga: null,
  stok: null,
  kategori: '',
  unggulan: false
})
const pesanError = ref('')

function tambahProduk() {
  if (!formBaru.nama || !formBaru.kategori || !formBaru.harga) {
    pesanError.value = 'Nama, kategori, dan harga wajib diisi.'
    return
  }
  pesanError.value = ''

  const idBaru = daftarProduk.length
    ? Math.max(...daftarProduk.map(p => p.id)) + 1
    : 1

  daftarProduk.push({
    id: idBaru,
    nama: formBaru.nama,
    kategori: formBaru.kategori,
    harga: formBaru.harga,
    stok: formBaru.stok || 0,
    emoji: formBaru.unggulan ? '⭐' : '📦'
  })

  formBaru.nama = ''
  formBaru.harga = null
  formBaru.stok = null
  formBaru.kategori = ''
  formBaru.unggulan = false
}
</script>

<template>
  <div id="app">
    <!-- Tahap 1: Banner sambutan -->
    <div v-if="tampilkanBanner" class="banner">
      <p>🎉 Selamat datang di TokoKu! Nikmati promo spesial hari ini.</p>
      <button @click="tampilkanBanner = false">✕ Tutup</button>
    </div>

    <StoreHeader
      :nama-toko="namaToko"
      tagline="Belanja mudah, harga bersahabat"
      :jumlah-keranjang="jumlahKeranjang"
    />

    <!-- Tahap 1: Kupon sekali klaim -->
    <div class="kupon-box">
      <button v-if="!kuponDiklaim" @click.once="klaimKupon">
        🎁 Klaim Kupon Diskon 10%
      </button>
      <p v-else class="kupon-info">
        Kupon berhasil diklaim! Gunakan kode: <strong>TOKOKU10</strong>
      </p>
    </div>

    <!-- Pencarian + Tahap 2: toggle filter -->
    <div class="search-bar">
      <input
        v-model="kataKunci"
        type="text"
        placeholder="Cari produk..."
      />
      <button @click="tampilkanFilter = !tampilkanFilter">
        {{ tampilkanFilter ? 'Sembunyikan Filter' : 'Tampilkan Filter' }}
      </button>
    </div>

    <div v-show="tampilkanFilter" class="filter-panel">
      <label>
        <input type="checkbox" v-model="hanyaStokTersedia" />
        Hanya tampilkan produk yang tersedia
      </label>
    </div>

    <h2 class="section-title">Semua Produk</h2>
    <div class="product-grid">
      <div
        v-for="produk in daftarProduk.filter(p =>
          p.nama.toLowerCase().includes(kataKunci.toLowerCase()) &&
          (!hanyaStokTersedia || p.stok > 0)
        )"
        :key="produk.id"
        class="product-slot"
        :class="{ disorot: produkDisorot === produk.id }"
        @mouseover="produkDisorot = produk.id"
        @mouseleave="produkDisorot = null"
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

    <!-- Tahap 3: Nested v-for per kategori -->
    <h2 class="section-title">Belanja per Kategori</h2>
    <div v-for="kategori in kategoriList" :key="kategori" class="kategori-group">
      <h3>{{ kategori }}</h3>
      <div
        v-for="(produk, index) in daftarProduk.filter(p => p.kategori === kategori)"
        :key="produk.id"
        class="produk-bernomor"
      >
        <span class="nomor">{{ index + 1 }}.</span>
        <ProductCard
          :nama="produk.nama"
          :kategori="produk.kategori"
          :harga="produk.harga"
          :stok="produk.stok"
          :emoji="produk.emoji"
        />
      </div>
    </div>

    <!-- Tahap 4: Form tambah produk -->
    <h2 class="section-title">Tambah Produk Baru</h2>
    <form @submit.prevent="tambahProduk" class="form-tambah">
      <p v-if="pesanError" class="error">{{ pesanError }}</p>

      <label>Nama Produk</label>
      <input v-model.trim="formBaru.nama" type="text" placeholder="Nama produk" />

      <label>Harga (Rp)</label>
      <input v-model.number="formBaru.harga" type="number" placeholder="0" />

      <label>Stok</label>
      <input v-model.number="formBaru.stok" type="number" placeholder="0" />

      <label>Kategori</label>
      <div class="radio-group">
        <label v-for="kat in kategoriList" :key="kat">
          <input type="radio" :value="kat" v-model="formBaru.kategori" />
          {{ kat }}
        </label>
      </div>

      <label class="checkbox-label">
        <input type="checkbox" v-model="formBaru.unggulan" />
        Tandai sebagai produk unggulan
      </label>

      <button type="submit">Tambah Produk</button>
    </form>
  </div>
</template>

<style>
body { font-family: sans-serif; margin: 0; background: #f8fafc; }

.banner {
  background: #fef3c7; color: #92400e; padding: 12px 24px;
  display: flex; justify-content: space-between; align-items: center;
}
.banner button { border: none; background: transparent; cursor: pointer; font-size: 16px; }

.kupon-box { padding: 12px 24px; }
.kupon-box button {
  background: #7c3aed; color: #fff; border: none;
  padding: 10px 16px; border-radius: 8px; cursor: pointer;
}
.kupon-info { color: #7c3aed; font-weight: 600; }

.search-bar { padding: 16px 24px 0; display: flex; gap: 8px; }
.search-bar input {
  flex: 1; max-width: 320px; padding: 8px 12px;
  border: 1px solid #cbd5e1; border-radius: 8px;
}
.search-bar button {
  padding: 8px 12px; border-radius: 8px; border: 1px solid #cbd5e1;
  background: #fff; cursor: pointer;
}

.filter-panel { padding: 8px 24px; color: #475569; }

.section-title { padding: 0 24px; margin-top: 24px; }

.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
  padding: 0 24px 24px;
}
.product-slot { transition: box-shadow 0.15s; border-radius: 12px; }
.product-slot.disorot { box-shadow: 0 0 0 3px #0f172a; }
.actions { display: flex; gap: 8px; margin-top: 8px; }
.actions button {
  flex: 1; padding: 8px; border: none; border-radius: 8px;
  background: #0f172a; color: #fff; cursor: pointer;
}
.actions button:disabled { background: #cbd5e1; cursor: not-allowed; }
.actions .restock { background: #f59e0b; }

.kategori-group { padding: 0 24px 16px; }
.produk-bernomor { display: flex; align-items: center; gap: 8px; margin-bottom: 8px; }
.produk-bernomor .nomor { font-weight: 700; width: 20px; }
.produk-bernomor .product-card { flex: 1; }

.form-tambah {
  max-width: 360px; margin: 0 24px 32px; padding: 16px;
  border: 1px solid #e2e8f0; border-radius: 12px;
  display: flex; flex-direction: column; gap: 6px;
}
.form-tambah input[type="text"],
.form-tambah input[type="number"] {
  padding: 8px; border: 1px solid #cbd5e1; border-radius: 6px;
}
.form-tambah .radio-group { display: flex; gap: 12px; }
.form-tambah .checkbox-label { display: flex; align-items: center; gap: 6px; }
.form-tambah button[type="submit"] {
  margin-top: 8px; padding: 10px; background: #16a34a; color: #fff;
  border: none; border-radius: 8px; cursor: pointer;
}
.error { color: #dc2626; font-size: 13px; }
</style>
```

---

## 6. Tantangan Bonus (Opsional, tidak disertai kunci jawaban)

1. Tambahkan `@keyup.enter` pada kolom pencarian agar menampilkan notifikasi "Menampilkan hasil untuk: ..." saat Enter ditekan.
2. Gunakan `v-else-if` tambahan untuk status **"Baru Tiba"** jika sebuah produk memiliki properti `baru: true` (perlu menambah field baru di data).
3. Amati bahwa ekspresi `.filter(...)` kini dipakai di **tiga tempat berbeda** (grid utama, grup kategori, dan — jika bonus 1 dikerjakan — pencarian). Ini sinyal kuat bahwa `computed()` (materi hari berikutnya) akan sangat membantu merapikan kode ini.
