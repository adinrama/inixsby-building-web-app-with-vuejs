# Studi Kasus Proyek — Hari 3: Vue 3 Dasar Terpadu
## "TaskFlow" — Aplikasi Manajemen Tugas dengan Timer Fokus & Tema Adaptif

---

## 1. Informasi Umum

| Item | Keterangan |
|---|---|
| Level | Intermediate (lanjutan dari materi Setup Vite, Template, Props, Event Handling) |
| Estimasi Waktu | 90–120 menit |
| Metode Kerja | Individu / Berpasangan |
| Tools | Vue 3 (Composition API), Vite, `<script setup>` |
| Materi yang Diuji | Lifecycle Hooks, Template Refs, Computed, Watch, Provide/Inject, Custom Composable, Styling |

---

## 2. Skenario Proyek

Tim produk meminta dibuatkan aplikasi internal bernama **TaskFlow** untuk membantu peserta training mengelola tugas harian sekaligus menjaga fokus belajar menggunakan teknik Pomodoro (timer fokus). Aplikasi harus:

- Menampilkan daftar tugas yang bisa ditambah, ditandai selesai, dan dihapus.
- Menampilkan ringkasan progres (statistik) secara otomatis dari data tugas.
- Menyediakan timer fokus yang berjalan mundur dan memberi notifikasi saat waktu habis.
- Mendukung mode gelap/terang yang bisa diakses oleh komponen mana pun tanpa oper-mengoper props.
- Kode timer harus dapat dipakai ulang (reusable) di komponen lain jika suatu saat dibutuhkan.

Studi kasus ini **sengaja dirancang agar keenam topik hari ini saling terhubung** dalam satu aplikasi kecil yang utuh — bukan berdiri sendiri-sendiri.

---

## 3. Requirement Fungsional (Checklist)

Tandai ✅ setiap requirement berikut sudah terpenuhi di project Anda:

**A. Lifecycle Hooks**
- [ ] Input tugas baru otomatis ter-*focus* begitu halaman selesai dimuat (`onMounted`).
- [ ] Timer interval (`setInterval`) dibersihkan otomatis saat komponen timer dilepas dari DOM (`onUnmounted`), agar tidak terjadi memory leak.

**B. Template Refs**
- [ ] Elemen `<input>` tugas baru diakses langsung dari script menggunakan `ref`, bukan lewat `document.querySelector`.

**C. Computed Properties**
- [ ] Total tugas, jumlah tugas selesai, dan persentase progres dihitung otomatis (bukan disimpan manual di `data`/`ref` terpisah yang di-update manual).
- [ ] Daftar tugas yang ditampilkan bisa difilter (Semua / Aktif / Selesai) menggunakan `computed`.

**D. Watchers**
- [ ] Setiap perubahan pada daftar tugas otomatis disimpan ke `localStorage` (`watch` dengan `deep: true`).
- [ ] Saat hitung mundur timer mencapai 0, muncul notifikasi (side effect yang **tidak boleh** ditaruh di `computed`).

**E. Provide & Inject**
- [ ] Status tema (gelap/terang) disediakan (`provide`) satu kali di komponen root, dan diambil (`inject`) langsung oleh komponen anak/cucu tanpa props drilling.

**F. Custom Composable**
- [ ] Logika timer (start, pause, reset, format waktu) dibungkus dalam file composable tersendiri bernama `useCountdownTimer.js`, bukan ditulis langsung di dalam komponen.

**G. Styling**
- [ ] Menggunakan `scoped style` di setiap komponen.
- [ ] Menggunakan class binding dinamis `:class="{ ... }"` untuk status selesai/tema.
- [ ] Menggunakan style binding dinamis `:style="{ ... }"` untuk lebar progress bar.

---

## 4. Struktur Folder yang Diharapkan

```
src/
├── App.vue
├── main.js
├── assets/
│   └── main.css
├── composables/
│   └── useCountdownTimer.js
└── components/
    ├── AppHeader.vue
    ├── TaskInput.vue
    ├── TaskStats.vue
    ├── TaskList.vue
    ├── TaskItem.vue
    └── PomodoroTimer.vue
```

---

## 5. Soal Latihan Bertahap

Kerjakan berurutan. Kerangka (starter code) diberikan dengan tanda `// TODO` — isi bagian yang kosong.

### Tahap 1 — Composable Timer (`composables/useCountdownTimer.js`)

Buat fungsi `useCountdownTimer(initialMinutes)` yang mengembalikan:
`secondsLeft`, `isRunning`, `formattedTime` (format `MM:SS`), serta fungsi `start()`, `pause()`, `reset()`.
Pastikan `setInterval` dibersihkan otomatis lewat lifecycle hook yang sesuai.

```js
import { ref, computed, onUnmounted } from 'vue'

export function useCountdownTimer(initialMinutes = 25) {
  const secondsLeft = ref(initialMinutes * 60)
  const isRunning = ref(false)
  let intervalId = null

  const formattedTime = computed(() => {
    // TODO: ubah secondsLeft.value menjadi format "MM:SS"
  })

  function start() {
    // TODO: jalankan setInterval yang mengurangi secondsLeft setiap 1 detik
  }

  function pause() {
    // TODO: hentikan interval dan set isRunning ke false
  }

  function reset(minutes = initialMinutes) {
    // TODO: panggil pause() lalu kembalikan secondsLeft ke nilai awal
  }

  // TODO: pastikan interval dibersihkan saat komponen pemakai composable ini di-unmount

  return { secondsLeft, isRunning, formattedTime, start, pause, reset }
}
```

### Tahap 2 — Root Component (`App.vue`)

- Definisikan state `tasks` (array of `{ id, text, done }`) dan `filterStatus`.
- Buat 3 `computed`: `filteredTasks`, `completedCount`, `progressPercentage`.
- Buat `watch` pada `tasks` (deep) untuk menyimpan ke `localStorage`.
- Sediakan (`provide`) objek tema `{ isDark, toggleTheme }` untuk seluruh keturunan komponen.

### Tahap 3 — `TaskInput.vue`

- Buat `ref` untuk elemen `<input>` dan pasang `ref="inputRef"` di template.
- Gunakan `onMounted` agar input otomatis fokus saat halaman dimuat.
- Emit event `add-task` berisi teks tugas baru saat form disubmit.

### Tahap 4 — `TaskStats.vue`

- Terima props `total`, `completed`, `percentage` dari parent.
- Tampilkan progress bar dengan lebar dinamis menggunakan `:style`.

### Tahap 5 — `TaskList.vue` & `TaskItem.vue`

- `TaskList` menerima props `tasks` dan me-*render* `TaskItem` untuk tiap item.
- `TaskItem` meng-*inject* tema untuk pewarnaan, dan menggunakan `:class` untuk status `done`.

### Tahap 6 — `PomodoroTimer.vue`

- Panggil `useCountdownTimer(25)`.
- Tambahkan `watch` pada `secondsLeft`: jika mencapai `0`, tampilkan notifikasi (`alert` atau elemen UI).

### Tahap 7 — `AppHeader.vue`

- `inject` tema, tampilkan tombol untuk memanggil `toggleTheme`.

---

## 6. Rubrik Penilaian

| Aspek | Bobot |
|---|---|
| Lifecycle Hooks digunakan tepat (mount + cleanup) | 15% |
| Template Refs berfungsi (autofocus) | 10% |
| Computed dipakai untuk nilai turunan (bukan watch) | 20% |
| Watch dipakai untuk side effect (bukan sekadar gabung data) | 20% |
| Provide/Inject berjalan tanpa props drilling | 15% |
| Composable dapat dipakai ulang dan mandiri | 15% |
| Styling scoped + binding dinamis rapi | 5% |

---
