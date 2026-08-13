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

## 7. Kunci Jawaban Lengkap

### `composables/useCountdownTimer.js`

```js
import { ref, computed, onUnmounted } from 'vue'

export function useCountdownTimer(initialMinutes = 25) {
  const secondsLeft = ref(initialMinutes * 60)
  const isRunning = ref(false)
  let intervalId = null

  const formattedTime = computed(() => {
    const m = Math.floor(secondsLeft.value / 60).toString().padStart(2, '0')
    const s = (secondsLeft.value % 60).toString().padStart(2, '0')
    return `${m}:${s}`
  })

  function start() {
    if (isRunning.value) return
    isRunning.value = true
    intervalId = setInterval(() => {
      if (secondsLeft.value > 0) {
        secondsLeft.value--
      } else {
        pause()
      }
    }, 1000)
  }

  function pause() {
    isRunning.value = false
    clearInterval(intervalId)
    intervalId = null
  }

  function reset(minutes = initialMinutes) {
    pause()
    secondsLeft.value = minutes * 60
  }

  onUnmounted(() => {
    clearInterval(intervalId)
  })

  return { secondsLeft, isRunning, formattedTime, start, pause, reset }
}
```

### `App.vue`

```vue
<script setup>
import { ref, computed, provide, watch } from 'vue'
import AppHeader from './components/AppHeader.vue'
import TaskInput from './components/TaskInput.vue'
import TaskStats from './components/TaskStats.vue'
import TaskList from './components/TaskList.vue'
import PomodoroTimer from './components/PomodoroTimer.vue'

// ---------- Provide/Inject: Tema ----------
const isDark = ref(false)
function toggleTheme() {
  isDark.value = !isDark.value
}
provide('theme', { isDark, toggleTheme })

// ---------- State Tugas ----------
const tasks = ref([
  { id: 1, text: 'Pelajari Lifecycle Hooks', done: true },
  { id: 2, text: 'Pelajari Template Refs', done: false }
])
const filterStatus = ref('all') // all | active | completed

function addTask(text) {
  tasks.value.push({ id: Date.now(), text, done: false })
}
function toggleTask(id) {
  const task = tasks.value.find(t => t.id === id)
  if (task) task.done = !task.done
}
function removeTask(id) {
  tasks.value = tasks.value.filter(t => t.id !== id)
}

// ---------- Computed ----------
const filteredTasks = computed(() => {
  if (filterStatus.value === 'active') return tasks.value.filter(t => !t.done)
  if (filterStatus.value === 'completed') return tasks.value.filter(t => t.done)
  return tasks.value
})
const totalTasks = computed(() => tasks.value.length)
const completedCount = computed(() => tasks.value.filter(t => t.done).length)
const progressPercentage = computed(() => {
  if (totalTasks.value === 0) return 0
  return Math.round((completedCount.value / totalTasks.value) * 100)
})

// ---------- Watch: auto-save ----------
watch(
  tasks,
  (newTasks) => {
    localStorage.setItem('taskflow-tasks', JSON.stringify(newTasks))
  },
  { deep: true }
)
</script>

<template>
  <div class="app-container" :class="{ dark: isDark }">
    <AppHeader />
    <TaskInput @add-task="addTask" />
    <TaskStats
      :total="totalTasks"
      :completed="completedCount"
      :percentage="progressPercentage"
    />
    <div class="filter-buttons">
      <button @click="filterStatus = 'all'">Semua</button>
      <button @click="filterStatus = 'active'">Aktif</button>
      <button @click="filterStatus = 'completed'">Selesai</button>
    </div>
    <TaskList :tasks="filteredTasks" @toggle-task="toggleTask" @remove-task="removeTask" />
    <PomodoroTimer />
  </div>
</template>

<style scoped>
.app-container {
  max-width: 480px;
  margin: 0 auto;
  padding: 24px;
  font-family: sans-serif;
  transition: background-color 0.2s linear, color 0.2s linear;
}
.app-container.dark {
  background-color: #1e1e2f;
  color: #f1f1f1;
}
.filter-buttons {
  display: flex;
  gap: 8px;
  margin: 16px 0;
}
</style>
```

### `components/AppHeader.vue`

```vue
<script setup>
import { inject } from 'vue'
const theme = inject('theme')
</script>

<template>
  <header class="app-header">
    <h1>📋 TaskFlow</h1>
    <button class="theme-toggle" @click="theme.toggleTheme">
      {{ theme.isDark.value ? '☀️ Mode Terang' : '🌙 Mode Gelap' }}
    </button>
  </header>
</template>

<style scoped>
.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.theme-toggle {
  border: none;
  border-radius: 6px;
  padding: 6px 12px;
  cursor: pointer;
}
</style>
```

### `components/TaskInput.vue`

```vue
<script setup>
import { ref, onMounted } from 'vue'

const emit = defineEmits(['add-task'])
const newTaskText = ref('')
const inputRef = ref(null)

function handleSubmit() {
  const trimmed = newTaskText.value.trim()
  if (!trimmed) return
  emit('add-task', trimmed)
  newTaskText.value = ''
  inputRef.value.focus()
}

onMounted(() => {
  inputRef.value.focus()
})
</script>

<template>
  <form class="task-input" @submit.prevent="handleSubmit">
    <input
      ref="inputRef"
      v-model="newTaskText"
      type="text"
      placeholder="Tulis tugas baru..."
    />
    <button type="submit">Tambah</button>
  </form>
</template>

<style scoped>
.task-input {
  display: flex;
  gap: 8px;
  margin: 16px 0;
}
.task-input input {
  flex: 1;
  padding: 8px;
}
</style>
```

### `components/TaskStats.vue`

```vue
<script setup>
defineProps({
  total: { type: Number, required: true },
  completed: { type: Number, required: true },
  percentage: { type: Number, required: true }
})
</script>

<template>
  <div class="task-stats">
    <p>{{ completed }} dari {{ total }} tugas selesai ({{ percentage }}%)</p>
    <div class="progress-bar-track">
      <div class="progress-bar-fill" :style="{ width: percentage + '%' }"></div>
    </div>
  </div>
</template>

<style scoped>
.progress-bar-track {
  width: 100%;
  height: 10px;
  background: #e0e0e0;
  border-radius: 6px;
  overflow: hidden;
}
.progress-bar-fill {
  height: 100%;
  background: #16c0b0;
  transition: width 0.3s ease;
}
</style>
```

### `components/TaskList.vue`

```vue
<script setup>
import TaskItem from './TaskItem.vue'

defineProps({ tasks: { type: Array, required: true } })
defineEmits(['toggle-task', 'remove-task'])
</script>

<template>
  <ul class="task-list">
    <li v-if="tasks.length === 0" class="empty-state">Tidak ada tugas.</li>
    <TaskItem
      v-for="task in tasks"
      :key="task.id"
      :task="task"
      @toggle="$emit('toggle-task', task.id)"
      @remove="$emit('remove-task', task.id)"
    />
  </ul>
</template>

<style scoped>
.task-list {
  list-style: none;
  padding: 0;
}
.empty-state {
  color: #999;
  font-style: italic;
}
</style>
```

### `components/TaskItem.vue`

```vue
<script setup>
import { inject } from 'vue'

defineProps({ task: { type: Object, required: true } })
defineEmits(['toggle', 'remove'])
const theme = inject('theme')
</script>

<template>
  <li class="task-item" :class="{ done: task.done, dark: theme.isDark.value }">
    <input type="checkbox" :checked="task.done" @change="$emit('toggle')" />
    <span class="task-text">{{ task.text }}</span>
    <button class="remove-btn" @click="$emit('remove')">🗑️</button>
  </li>
</template>

<style scoped>
.task-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 0;
  border-bottom: 1px solid #eee;
}
.task-item.done .task-text {
  text-decoration: line-through;
  opacity: 0.6;
}
.task-item.dark {
  border-color: #444;
}
.remove-btn {
  margin-left: auto;
  border: none;
  background: transparent;
  cursor: pointer;
}
</style>
```

### `components/PomodoroTimer.vue`

```vue
<script setup>
import { watch, inject } from 'vue'
import { useCountdownTimer } from '../composables/useCountdownTimer'

const { secondsLeft, isRunning, formattedTime, start, pause, reset } = useCountdownTimer(25)
const theme = inject('theme')

watch(secondsLeft, (newVal) => {
  if (newVal === 0) {
    alert('Waktu fokus selesai! Saatnya istirahat sejenak.')
  }
})
</script>

<template>
  <div class="timer-card" :class="{ dark: theme.isDark.value }">
    <h3>⏱️ Fokus Timer</h3>
    <p class="time-display">{{ formattedTime }}</p>
    <div class="timer-controls">
      <button @click="start" :disabled="isRunning">Mulai</button>
      <button @click="pause" :disabled="!isRunning">Jeda</button>
      <button @click="reset()">Reset</button>
    </div>
  </div>
</template>

<style scoped>
.timer-card {
  margin-top: 24px;
  padding: 16px;
  border-radius: 10px;
  background: #f5f5f5;
  text-align: center;
}
.timer-card.dark {
  background: #2a2a3d;
}
.time-display {
  font-size: 2.5rem;
  font-weight: 700;
  margin: 8px 0;
}
.timer-controls {
  display: flex;
  justify-content: center;
  gap: 8px;
}
</style>
```

### `main.js`

```js
import { createApp } from 'vue'
import App from './App.vue'
import './assets/main.css'

createApp(App).mount('#app')
```

---

## 8. Catatan Penjelasan Kunci Jawaban

| File | Konsep yang Ditunjukkan |
|---|---|
| `useCountdownTimer.js` | **Custom Composable** membungkus state + logic timer; **Lifecycle** (`onUnmounted`) dipasang *di dalam* composable, bukan di komponen pemakai — ini pola yang direkomendasikan agar cleanup tidak terlupa di mana pun composable dipakai. |
| `App.vue` | **Computed** untuk nilai turunan (`filteredTasks`, `progressPercentage`, dll — wajib `return`, tanpa side effect); **Watch** untuk side effect (`localStorage.setItem`, operasi yang **tidak boleh** dilakukan di `computed`); **Provide** dipanggil sekali di root. |
| `TaskInput.vue` | **Template Ref** (`ref="inputRef"`) untuk akses DOM langsung; **Lifecycle** `onMounted` memastikan elemen sudah ada di DOM sebelum dipanggil `.focus()`. |
| `TaskItem.vue`, `PomodoroTimer.vue`, `AppHeader.vue` | **Inject** mengambil data tema tanpa props drilling — ketiganya berada di level kedalaman berbeda dari `App.vue` tapi tetap bisa mengakses tema langsung. |
| `PomodoroTimer.vue` | **Watch** dipakai untuk memicu efek samping (notifikasi) saat `secondsLeft` mencapai `0` — bukan tugas `computed` karena ini aksi, bukan nilai turunan. |
| Semua komponen `.vue` | **Styling**: `<style scoped>` di setiap file mencegah tabrakan class antarkomponen; `:class="{ dark: ... }"` dan `:style="{ width: ... }"` menunjukkan binding style/class dinamis berbasis reaktivitas. |

---

## 9. Tantangan Bonus (Opsional)

1. Buat composable kedua bernama `useLocalStorage(key, defaultValue)` yang bersifat generik, lalu gunakan untuk menyimpan preferensi tema (`isDark`) agar tetap tersimpan setelah browser di-refresh.
2. Tambahkan fitur pencarian tugas menggunakan `watch` pada input pencarian dengan teknik *debounce* sederhana (`setTimeout` + `clearTimeout`).
3. Gunakan `beforeRouteEnter`-style thinking (walau belum pakai Vue Router): terapkan `onBeforeUnmount` untuk menampilkan konfirmasi jika ada timer yang masih berjalan saat komponen `PomodoroTimer` akan dilepas.
